---
PlayerName: Evan S.
Level: 1
Experience: 0
CurrentHitPoints: 11
Strength: 7, 0
Dexterity: 6, 0
Constitution: 4, 0
Intelligence: 2, 0
Wisdom: 2, 0
Charisma: 3, 0
AcBonus: 0
InitiativeBonus: 0
SpeedBonus: 0
TemporaryHitPoints: 0
DeathSaveSuccesses: 0
DeathSaveFailures: 0
---

```dataviewjs
const file = dv.current().file.name;
dv.paragraph(`![[${file}.png|250]]`);
```

```dataviewjs
const file = dv.current().file;
const page = dv.page(file.path);
const files = app.vault.getMarkdownFiles();
let content = await app.vault.read(app.vault.getAbstractFileByPath(file.path));
const lines = content.split("\n");

const level = page.level ?? 1;
const playerName = page.PlayerName ?? "Player Name";

// --- Experience Tracker ---
const xpTable = [0, 300, 900, 2700, 6500, 14000, 23000, 34000, 48000, 64000,
                 85000, 100000, 120000, 140000, 165000, 195000, 225000, 265000,
                 305000, 355000];

const xp = page.experience ?? 0;
const currLevelXp = xpTable[level - 1] ?? 0;
const nextLevelXp = xpTable[level] ?? (xpTable[xpTable.length - 1] + 10000);
const progress = xp - currLevelXp;
const required = nextLevelXp - currLevelXp;

const xpColor = xp >= nextLevelXp ? "green" : progress < 0 ? "red" : "inherit";

// Extract section links
async function extractLinkFromHeading(headingLabel) {
    const start = lines.findIndex(line => line.trim() === headingLabel);
    if (start === -1) return null;
    for (let i = start + 1; i < lines.length; i++) {
        const line = lines[i].trim();
        if (line.startsWith("#") || line === "") break;
        const match = line.match(/\[\[([^\]]+)\]\]/);
        if (match) return match[1];
    }
    return null;
}

const classNote = await extractLinkFromHeading("### **Class**");
const backgroundNote = await extractLinkFromHeading("### **Background**");
const raceNote = await extractLinkFromHeading("### **Race**");
const alignmentNote = await extractLinkFromHeading("### **Alignment**");

const classLink = classNote ? dv.fileLink(classNote) : "—";
const backgroundLink = backgroundNote ? dv.fileLink(backgroundNote) : "—";
const raceLink = raceNote ? dv.fileLink(raceNote) : "—";
const alignmentLink = alignmentNote ? dv.fileLink(alignmentNote) : "—";

// Parse equipped items from inventory
const invStart = lines.findIndex(line => line.trim() === "### **Inventory**");
let equippedItems = [];

if (invStart !== -1) {
    for (let i = invStart + 1; i < lines.length; i++) {
        const line = lines[i].trim();
        if (line.startsWith("#") || line === "") break;
        if (line.startsWith("- [x]")) {
            const match = line.match(/\[\[([^\]]+)\]\]/);
            if (match) equippedItems.push(match[1]);
        }
    }
}

const equippedPaths = equippedItems.map(name =>
  files.find(f => f.basename === name)
).filter(f => f);

// Equipment bonuses
const bonusFields = [
  "StrengthBonus", "DexterityBonus", "ConstitutionBonus",
  "IntelligenceBonus", "WisdomBonus", "CharismaBonus"
];

let equipmentBonuses = Object.fromEntries(bonusFields.map(b => [b, 0]));
for (let file of equippedPaths) {
    if (file) {
        const cache = app.metadataCache.getFileCache(file);
        const fm = cache?.frontmatter ?? {};
        for (let field of bonusFields) {
            if (fm[field]) equipmentBonuses[field] += fm[field];
        }
    }
}

// Racial bonuses
const racialBonuses = {};
if (raceNote) {
    const raceFile = files.find(f => f.basename === raceNote);
    if (raceFile) {
        const raceContent = await app.vault.read(raceFile);
        const raceLines = raceContent.split("\n");
        const abilityHeading = "### **Ability**";
        const abilityStart = raceLines.findIndex(line => line.trim() === abilityHeading);

        if (abilityStart !== -1) {
            for (let i = abilityStart + 1; i < raceLines.length; i++) {
                const line = raceLines[i].trim();
                if (line.startsWith("#") || line === "") break;
                const match = line.match(/\+(\d+)\s+\[\[([^\]]+)\]\]/);
                if (match) {
                    const bonus = parseInt(match[1]);
                    const ability = match[2];
                    racialBonuses[ability] = (racialBonuses[ability] ?? 0) + bonus;
                }
            }
        }
    }
}

// Final ability scores
const abilityNames = ["Strength", "Dexterity", "Constitution", "Intelligence", "Wisdom", "Charisma"];
let abilityStatus = Object.fromEntries(abilityNames.map(a => [a, null]));
const finalAbilities = {};

for (let file of equippedPaths) {
    if (file) {
        const cache = app.metadataCache.getFileCache(file);
        const fm = cache?.frontmatter ?? {};
        for (let ability of abilityNames) {
            const val = fm[ability];
            if (val?.includes("Advantage")) abilityStatus[ability] = "Advantage";
            else if (val?.includes("Disadvantage")) abilityStatus[ability] = "Disadvantage";
        }
    }
}

for (let ability of abilityNames) {
    const [picks, levelUp] = (page[ability] ?? "0,0").split(",").map(Number);
    const racial = racialBonuses[ability] ?? 0;
    const equipBonus = equipmentBonuses[ability + "Bonus"] ?? 0;
    const score = 8 + picks + levelUp + racial + equipBonus;
    const modifier = Math.floor((score - 10) / 2);
    finalAbilities[ability] = { score, modifier };
}

// Display
dv.header(2, file.link);
dv.paragraph(`**Class & Level**: ${classLink} ${level}`);
dv.paragraph(`**Experience**: <span style="color:${xpColor}">${progress} / ${required}</span>`);
dv.paragraph(`**Background**: ${backgroundLink} | **Race**: ${raceLink}`);
dv.paragraph(`**Alignment**: ${alignmentLink} | **Player**: ${playerName}`);

dv.header(3, "Ability Scores");
dv.table(["Ability", "Score", "Modifier"],
    abilityNames.map(ability => {
        const { score, modifier } = finalAbilities[ability];
        return [`[[${ability}]]`, score, (() => {
    const modText = (modifier >= 0 ? "+" : "") + modifier;
    const status = abilityStatus[ability];

    if (status === "Advantage") {
        return `<span style="background-color: rgba(0,255,0,0.2); color: green; padding: 2px 4px; border-radius: 4px;">${modText}</span>`;
    }
    if (status === "Disadvantage") {
        return `<span style="background-color: rgba(255,0,0,0.2); color: red; padding: 2px 4px; border-radius: 4px;">${modText}</span>`;
    }
    return modText;})()];
    })
);
```

```dataviewjs
const file = dv.current().file;
const page = dv.page(file.path);
const files = app.vault.getMarkdownFiles();
let content = await app.vault.read(app.vault.getAbstractFileByPath(file.path));
const lines = content.split("\n");

const level = page.level ?? 1;
const proficiencyBonus = 2 + Math.floor((level - 1) / 4);
const abilityNames = ["Strength", "Dexterity", "Constitution", "Intelligence", "Wisdom", "Charisma"];

// --- Extract Section Link from Character Note ---
function extractLinkFromHeading(headingLabel) {
    const start = lines.findIndex(line => line.trim() === headingLabel);
    if (start === -1) return null;
    for (let i = start + 1; i < lines.length; i++) {
        const line = lines[i].trim();
        if (line.startsWith("#") || line === "") break;
        const match = line.match(/\[\[([^\]]+)\]\]/);
        if (match) return match[1];
    }
    return null;
}

// --- Parse Equipped Items ---
const invStart = lines.findIndex(line => line.trim() === "### **Inventory**");
let equippedItems = [];

if (invStart !== -1) {
    for (let i = invStart + 1; i < lines.length; i++) {
        const line = lines[i].trim();
        if (line.startsWith("#") || line === "") break;
        if (line.startsWith("- [x]")) {
            const match = line.match(/\[\[([^\]]+)\]\]/);
            if (match) equippedItems.push(match[1]);
        }
    }
}

// --- Save Advantage/Disadvantage from Equipped Items ---
let saveStatus = {};
for (let itemName of equippedItems) {
    const itemFile = files.find(f => f.basename === itemName);
    if (!itemFile) continue;
    const fm = app.metadataCache.getFileCache(itemFile)?.frontmatter ?? {};

    for (let ability of abilityNames) {
        const key = `${ability}Save`;
        const val = fm[key];
        if (typeof val === "string") {
            if (val.includes("Advantage")) saveStatus[ability] = "Advantage";
            if (val.includes("Disadvantage")) saveStatus[ability] = "Disadvantage";
        }
    }
}

// --- Equipment Bonuses ---
const bonusFields = abilityNames.map(a => a + "Bonus");
let equipmentBonuses = Object.fromEntries(bonusFields.map(b => [b, 0]));

for (let itemName of equippedItems) {
    const itemFile = files.find(f => f.basename === itemName);
    if (itemFile) {
        const cache = app.metadataCache.getFileCache(itemFile);
        const fm = cache?.frontmatter ?? {};
        for (let field of bonusFields) {
            if (fm[field]) equipmentBonuses[field] += fm[field];
        }
    }
}

// --- Racial Bonuses ---
const raceNote = extractLinkFromHeading("### **Race**");
const racialBonuses = {};

if (raceNote) {
    const raceFile = files.find(f => f.basename === raceNote);
    if (raceFile) {
        const raceContent = await app.vault.read(raceFile);
        const raceLines = raceContent.split("\n");
        const abilityHeading = "### **Ability**";
        const abilityStart = raceLines.findIndex(line => line.trim() === abilityHeading);

        if (abilityStart !== -1) {
            for (let i = abilityStart + 1; i < raceLines.length; i++) {
                const line = raceLines[i].trim();
                if (line.startsWith("#") || line === "") break;
                const match = line.match(/\+(\d+)\s+\[\[([^\]]+)\]\]/);
                if (match) {
                    const bonus = parseInt(match[1]);
                    const ability = match[2];
                    racialBonuses[ability] = (racialBonuses[ability] ?? 0) + bonus;
                }
            }
        }
    }
}

// --- Saving Throw Proficiencies from Class Note ---
const classNote = extractLinkFromHeading("### **Class**");
let savingThrowProfs = [];

if (classNote) {
    const classFile = files.find(f => f.basename === classNote);
    if (classFile) {
        const classContent = await app.vault.read(classFile);
        const classLines = classContent.split("\n");
        const saveHeading = "### **Saving Throws**";
        const start = classLines.findIndex(line => line.trim() === saveHeading);

        if (start !== -1) {
            for (let i = start + 1; i < classLines.length; i++) {
                const line = classLines[i].trim();
                if (line.startsWith("#") || line === "") break;
                const match = line.match(/\[\[([^\]]+)\]\]/);
                if (match) savingThrowProfs.push(match[1]);
            }
        }
    }
}

// --- Calculate Final Abilities with Bonuses ---
const finalAbilities = {};

for (let ability of abilityNames) {
    const [picks, levelUp] = (page[ability] ?? "0,0").split(",").map(Number);
    const racial = racialBonuses[ability] ?? 0;
    const equip = equipmentBonuses[ability + "Bonus"] ?? 0;
    const score = 8 + picks + levelUp + racial + equip;
    const modifier = Math.floor((score - 10) / 2);
    finalAbilities[ability] = { score, modifier };
}

// --- Display Table ---
dv.header(3, `[[Saving Throws]] ([[Proficiency|Proficiency Bonus]]: +${proficiencyBonus})`);

dv.table(["Ability", "Bonus", "Proficient?"],
    abilityNames.map(ability => {
        const isProf = savingThrowProfs.includes(ability);
        const mod = finalAbilities[ability].modifier;
        const bonus = mod + (isProf ? proficiencyBonus : 0);
        return [
            `[[${ability}]]`,
            (() => {
    const text = (bonus >= 0 ? "+" : "") + bonus;
    const state = saveStatus[ability];
    if (state === "Advantage")
        return `<span style="background-color: rgba(0,255,0,0.2); color: green; padding:2px 4px; border-radius:4px;">${text}</span>`;
    if (state === "Disadvantage")
        return `<span style="background-color: rgba(255,0,0,0.2); color: red: padding:2px 4px; border-radius:4px;">${text}</span>`;
    return text;})(),
            isProf ? "✓" : ""
        ];
    })
);
```

```dataviewjs
const file = dv.current().file;
const page = dv.page(file.path);
const files = app.vault.getMarkdownFiles();
let content = await app.vault.read(app.vault.getAbstractFileByPath(file.path));
const lines = content.split("\n");

const level = page.level ?? 1;
const proficiencyBonus = 2 + Math.floor((level - 1) / 4);
const abilityNames = ["Strength", "Dexterity", "Constitution", "Intelligence", "Wisdom", "Charisma"];

// --- Map of Skills to Abilities ---
const skillMap = {
    "Acrobatics": "Dexterity", "Animal Handling": "Wisdom", "Arcana": "Intelligence",
    "Athletics": "Strength", "Deception": "Charisma", "History": "Intelligence",
    "Insight": "Wisdom", "Intimidation": "Charisma", "Investigation": "Intelligence",
    "Medicine": "Wisdom", "Nature": "Intelligence", "Perception": "Wisdom",
    "Performance": "Charisma", "Persuasion": "Charisma", "Religion": "Intelligence",
    "Sleight of Hand": "Dexterity", "Stealth": "Dexterity", "Survival": "Wisdom"
};

// --- Get equipped items from inventory ---
const invStart = lines.findIndex(line => line.trim() === "### **Inventory**");
let equippedItems = [];

if (invStart !== -1) {
    for (let i = invStart + 1; i < lines.length; i++) {
        const line = lines[i].trim();
        if (line.startsWith("#") || line === "") break;
        if (line.startsWith("- [x]")) {
            const match = line.match(/\[\[([^\]]+)\]\]/);
            if (match) equippedItems.push(match[1]);
        }
    }
}

// --- Equipment Bonuses ---
const bonusFields = abilityNames.map(a => a + "Bonus");
let equipmentBonuses = Object.fromEntries(bonusFields.map(b => [b, 0]));

for (let itemName of equippedItems) {
    const itemFile = files.find(f => f.basename === itemName);
    if (itemFile) {
        const cache = app.metadataCache.getFileCache(itemFile);
        const fm = cache?.frontmatter ?? {};
        for (let field of bonusFields) {
            if (fm[field]) equipmentBonuses[field] += fm[field];
        }
    }
}

// --- Skill Advantage/Disadvantage from equipped items ---
let skillStatus = {};

for (let itemName of equippedItems) {
    const itemFile = files.find(f => f.basename === itemName);
    if (itemFile) {
        const cache = app.metadataCache.getFileCache(itemFile);
        const fm = cache?.frontmatter ?? {};
        for (let skill in skillMap) {
            const value = fm[skill];
            if (typeof value === "string" && value.includes("Advantage")) skillStatus[skill] = "Advantage";
            if (typeof value === "string" && value.includes("Disadvantage")) skillStatus[skill] = "Disadvantage";
        }
    }
}

// --- Extract Race & Background links ---
function extractLinkFromHeading(headingLabel) {
    const start = lines.findIndex(line => line.trim() === headingLabel);
    if (start === -1) return null;
    for (let i = start + 1; i < lines.length; i++) {
        const line = lines[i].trim();
        if (line.startsWith("#") || line === "") break;
        const match = line.match(/\[\[([^\]]+)\]\]/);
        if (match) return match[1];
    }
    return null;
}

const raceNote = extractLinkFromHeading("### **Race**");
const backgroundNote = extractLinkFromHeading("### **Background**");

// --- Racial Bonuses ---
const racialBonuses = {};

if (raceNote) {
    const raceFile = files.find(f => f.basename === raceNote);
    if (raceFile) {
        const raceContent = await app.vault.read(raceFile);
        const raceLines = raceContent.split("\n");
        const abilityHeading = "### **Ability**";
        const abilityStart = raceLines.findIndex(line => line.trim() === abilityHeading);

        if (abilityStart !== -1) {
            for (let i = abilityStart + 1; i < raceLines.length; i++) {
                const line = raceLines[i].trim();
                if (line.startsWith("#") || line === "") break;
                const match = line.match(/\+(\d+)\s+\[\[([^\]]+)\]\]/);
                if (match) {
                    const bonus = parseInt(match[1]);
                    const ability = match[2];
                    racialBonuses[ability] = (racialBonuses[ability] ?? 0) + bonus;
                }
            }
        }
    }
}

// --- Final Abilities ---
const finalAbilities = {};

for (let ability of abilityNames) {
    const [picks, levelUp] = (page[ability] ?? "0,0").split(",").map(Number);
    const racial = racialBonuses[ability] ?? 0;
    const equip = equipmentBonuses[ability + "Bonus"] ?? 0;
    const score = 8 + picks + levelUp + racial + equip;
    const modifier = Math.floor((score - 10) / 2);
    finalAbilities[ability] = { score, modifier };
}

// --- Collect Skill Proficiencies from multiple sources ---
let skillProfs = new Set(page.skillProficiencies ?? []);

// Selected Skills from character note
const selectedSkillsHeading = "##### **Selected Skills**";
const selStart = lines.findIndex(l => l.trim() === selectedSkillsHeading);
if (selStart !== -1) {
    for (let i = selStart + 1; i < lines.length; i++) {
        const ln = lines[i].trim();
        if (ln.startsWith("#") || ln === "") break;
        const match = ln.match(/\[\[([^\]]+)\]\]/);
        if (match) skillProfs.add(match[1]);
    }
}

// Background skills
if (backgroundNote) {
    const bgFile = files.find(f => f.basename === backgroundNote);
    if (bgFile) {
        const bgContent = await app.vault.read(bgFile);
        const bgLines = bgContent.split("\n");
        const bgHeading = "### **Skills**";
        const bgStart = bgLines.findIndex(l => l.trim() === bgHeading);
        if (bgStart !== -1) {
            for (let i = bgStart + 1; i < bgLines.length; i++) {
                const ln = bgLines[i].trim();
                if (ln.startsWith("#") || ln === "") break;
                const match = ln.match(/\[\[([^\]]+)\]\]/);
                if (match) skillProfs.add(match[1]);
            }
        }
    }
}

skillProfs = Array.from(skillProfs);

// --- Passive Wisdom ---
const passiveWisdom = 10 + finalAbilities["Wisdom"].modifier + (skillProfs.includes("Perception") ? proficiencyBonus : 0);

// --- Display Table ---
dv.header(3, `Skills (Passive Wisdom: ${passiveWisdom})`);

dv.table(["Skill", "Bonus", "Proficient?"], 
    Object.entries(skillMap).map(([skill, ability]) => {
        const isProf = skillProfs.includes(skill);
        const mod = finalAbilities[ability].modifier;
        const bonus = mod + (isProf ? proficiencyBonus : 0);

        const displayBonus = (() => {
            const text = (bonus >= 0 ? "+" : "") + bonus;
            const state = skillStatus[skill];
            if (state === "Advantage") return `<span style="background-color: color: green; rgba(0,255,0,0.2); padding:2px 4px; border-radius:4px;">${text}</span>`;
            if (state === "Disadvantage") return `<span style="background-color: rgba(255,0,0,0.2); color: red; padding:2px 4px; border-radius:4px;">${text}</span>`;
            return text;
        })();

        return [`[[${skill}]]`, displayBonus, isProf ? "✓" : ""];
    })
);
```

```dataviewjs
const file = dv.current().file;
const page = dv.page(file.path);
const files = app.vault.getMarkdownFiles();

let content = await app.vault.read(app.vault.getAbstractFileByPath(file.path));
const lines = content.split("\n");

const level = page.level ?? 1;
const abilityNames = ["Strength", "Dexterity", "Constitution", "Intelligence", "Wisdom", "Charisma"];

// --- Helper to extract a linked section ---
function extractLinkFromHeading(label) {
  const start = lines.findIndex(l => l.trim() === label);
  if (start === -1) return null;
  for (let i = start + 1; i < lines.length; i++) {
    const ln = lines[i].trim();
    if (ln.startsWith("#") || ln === "") break;
    const m = ln.match(/\[\[([^\]]+)\]\]/);
    if (m) return m[1];
  }
  return null;
}

const classNote = extractLinkFromHeading("### **Class**");
const raceNote = extractLinkFromHeading("### **Race**");

// --- Parse Equipped Items from Inventory ---
const invStart = lines.findIndex(l => l.trim() === "### **Inventory**");
let equippedItems = [];
if (invStart !== -1) {
  for (let i = invStart + 1; i < lines.length; i++) {
    const ln = lines[i].trim();
    if (ln.startsWith("#") || ln === "") break;
    if (ln.startsWith("- [x]")) {
      const m = ln.match(/\[\[([^\]]+)\]\]/);
      if (m) equippedItems.push(m[1]);
    }
  }
}

// --- Equipment-Based Bonuses ---
const bonusFields = [
  ...abilityNames.map(a => a + "Bonus"),
  "AcBonus", "SpeedBonus", "InitiativeBonus"
];
let equipmentBonuses = Object.fromEntries(bonusFields.map(f => [f, 0]));

for (let name of equippedItems) {
  const itemFile = files.find(f => f.basename === name);
  if (!itemFile) continue;
  const fm = app.metadataCache.getFileCache(itemFile)?.frontmatter ?? {};
  for (let f of bonusFields) {
    if (typeof fm[f] === "number") equipmentBonuses[f] += fm[f];
  }
}

// --- Racial Ability Bonuses ---
const racialBonuses = {};
if (raceNote) {
  const rFile = files.find(f => f.basename === raceNote);
  if (rFile) {
    const rLines = (await app.vault.read(rFile)).split("\n");
    const idx = rLines.findIndex(l => l.trim() === "### **Ability**");
    if (idx !== -1) {
      for (let i = idx + 1; i < rLines.length; i++) {
        const ln = rLines[i].trim();
        if (ln.startsWith("#") || ln === "") break;
        const m = ln.match(/\+(\d+)\s+\[\[([^\]]+)\]\]/);
        if (m) racialBonuses[m[2]] = (racialBonuses[m[2]] ?? 0) + parseInt(m[1]);
      }
    }
  }
}

// --- Compute Final Ability Scores ---
const finalAbilities = {};
for (let a of abilityNames) {
  const [picks, lvlUp] = (page[a] ?? "0,0").split(",").map(x => parseInt(x.trim()) || 0);
  const racial = racialBonuses[a] ?? 0;
  const equip = equipmentBonuses[a + "Bonus"] ?? 0;
  const score = 8 + picks + lvlUp + racial + equip;
  const mod = Math.floor((score - 10) / 2);
  finalAbilities[a] = { score, mod };
}

// --- Initiative ---
const initiative = (page.Initiative ?? finalAbilities["Dexterity"].mod) +
                   (page.InitiativeBonus ?? 0) +
                   (equipmentBonuses["InitiativeBonus"] ?? 0);

// --- Speed ---
let speed = "—";
const baseSpeed = parseInt((page.Speed ?? "").toString().replace(/[^\d]/g, "")) || null;
const spBonus = (page.SpeedBonus ?? 0) + (equipmentBonuses["SpeedBonus"] ?? 0);
if (baseSpeed) {
  speed = `${baseSpeed + spBonus} ft.`;
} else if (raceNote) {
  const rFile = files.find(f => f.basename === raceNote);
  if (rFile) {
    const rLines = (await app.vault.read(rFile)).split("\n");
    const idx = rLines.findIndex(l => l.trim() === "### **Movement**");
    if (idx !== -1) {
      for (let i = idx + 1; i < rLines.length; i++) {
        const ln = rLines[i].trim();
        if (ln.startsWith("#") || ln === "") break;
        const m = ln.match(/Base walking speed of (\d+)/i);
        if (m) speed = `${parseInt(m[1]) + spBonus} ft.`;
      }
    }
  }
}

// --- Armor Class ---
let baseAC = null;
let shield = false;
for (let name of equippedItems) {
  const f = files.find(fl => fl.basename === name);
  if (!f) continue;
  const p = f.path.toLowerCase();
  if (p.includes("/light armor/")) baseAC = 11 + finalAbilities["Dexterity"].mod;
  else if (p.includes("/medium armor/")) baseAC = 13 + Math.min(finalAbilities["Dexterity"].mod, 2);
  else if (p.includes("/heavy armor/")) baseAC = 16;
  if (p.includes("/shield/")) shield = true;
}
if (!baseAC) baseAC = 10 + finalAbilities["Dexterity"].mod;
const acBon = (page.AcBonus ?? 0) + (equipmentBonuses["AcBonus"] ?? 0);
const finalAC = page.Ac ?? (baseAC + (shield ? 2 : 0) + acBon);

// --- Hit Points & Hit Dice ---
let hpMax = page.MaxHitPoints ?? null;
let hitDice = "—";

if (hpMax === null && classNote) {
    const cFile = files.find(f => f.basename === classNote);
    if (cFile) {
        const cLines = (await app.vault.read(cFile)).split("\n");
        const idx = cLines.findIndex(l => l.trim() === "### **Hit Points**");

        let hitDieLink = null;
        let firstLevelBase = null;
        let firstLevelModAbility = null;
        let perLevelBase = null;
        let perLevelModAbility = null;

        if (idx !== -1) {
            for (let i = idx + 1; i < cLines.length; i++) {
                const ln = cLines[i].trim();
                if (ln.startsWith("#") || ln === "") break;

                const dieMatch = ln.match(/\*\*Hit Die:\*\*\s+\[\[\s*(d\d+)\s*\]\]/i);
                if (dieMatch) hitDieLink = `[[${dieMatch[1]}]]`;

                const firstMatch = ln.match(/\*\*HP at 1st Level:\*\*\s*(\d+)\s+\+\s+\[\[([^\]]+)\]\]/i);
                if (firstMatch) {
                    firstLevelBase = parseInt(firstMatch[1]);
                    firstLevelModAbility = firstMatch[2];
                }

                const perMatch = ln.match(/\*\*HP per Level After 1st:\*\*\s*(\d+)\s+\+\s+\[\[([^\]]+)\]\]/i);
                if (perMatch) {
                    perLevelBase = parseInt(perMatch[1]);
                    perLevelModAbility = perMatch[2];
                }
            }
        }

        if (
            hitDieLink &&
            firstLevelBase !== null &&
            firstLevelModAbility &&
            perLevelBase !== null &&
            perLevelModAbility &&
            finalAbilities[firstLevelModAbility] &&
            finalAbilities[perLevelModAbility]
        ) {
            const firstMod = finalAbilities[firstLevelModAbility].mod;
            const perMod = finalAbilities[perLevelModAbility].mod;
            hpMax = firstLevelBase + firstMod + (level - 1) * (perLevelBase + perMod);
            hitDice = `${level}${hitDieLink}`;
        }
    }
}

// --- Hit Points Display with Red at <20% ---
const curr = page.CurrentHitPoints ?? 0;
const temp = page.TemporaryHitPoints ?? 0;
const totalHP = curr + temp;
const maxHP = hpMax || 1;
const hpRatio = totalHP / maxHP;
const hpColor = hpRatio < 0.2 ? "red" : "inherit";
const hpDisplay = `<span style="color:${hpColor}">${totalHP} / ${maxHP}</span>`;

// --- Death Saves ---
const death = `✓ ${page.DeathSaveSuccesses ?? 0} / ✗ ${page.DeathSaveFailures ?? 0}`;

// --- Render Combat Stats ---
dv.header(3, "Combat Stats");
dv.table(["Stat", "Value"], [
  ["Armor Class", finalAC],
  ["Initiative", initiative],
  ["Speed", speed],
  ["Hit Points", hpDisplay],
  ["Hit Dice", hitDice],
  ["Death Saves", death]
]);
```

### **Inventory**
- [x] [[Chain Mail]]
- [x] [[Greataxe]]
- [ ] [[Shield]]
- [ ] [[Light Crossbow]]
- [ ] [[Bolts]] x20
- [x] [[Waterskin]] x0.2
- [ ] [[Crowbar]]
- [ ] [[Hammer]]
- [x] [[Hemp Rope]] x50 ft
- [x] [[Backpack]]
	- [ ] [[Pitons]] x10
	- [ ] [[Torch]] x10
	- [ ] [[Tinderbox]]
	- [ ] [[Rations]] x10
	- [ ] Pet Mouse
	- [ ] Small Knife
	- [ ] Map of [[Duram.jpeg|Duram]]
	- [ ] Token to Remember Parents
- [x] [[Common Clothes]]
- [x] [[Pouch]]
	- [x] [[gp]] x10
- [ ] [[Tar]]
```dataviewjs
const file = dv.current().file;
let content = await app.vault.read(app.vault.getAbstractFileByPath(file.path));
const lines = content.split("\n");

// --- Helper: Calculate accurate item weight ---
async function getWeight(name, count = 1) {
  const item = dv.page(name);
  if (item?.Weight) {
    const baseWeight = Number(item.Weight);
    const baseQty = Number(item.Quantity) || 1;
    return (baseWeight / baseQty) * count;
  }
  return 0;
}

// --- Parse inventory into nested structure ---
const heading = "### **Inventory**";
const start = lines.findIndex(l => l.trim() === heading);
let tree = [];
let stack = [];

if (start !== -1) {
  for (let i = start + 1; i < lines.length; i++) {
    const raw = lines[i];
    if (raw.trim().startsWith("###") || raw.trim() === "") break;

    const indent = raw.match(/^(\s*)/)[0].length;
    const line = raw.trim();
    const isChecked = line.startsWith("- [x]");
    const m = line.match(/- \[.\]\s+(?:\[\[)?([^\]\]]+)(?:\]\])?\s*(?:x(\d*\.?\d+))?/);
    if (!m) continue;

    const name = m[1];
    const count = m[2] ? parseFloat(m[2]) : 1;
    const node = { name, count, children: [], indent, isChecked };

    while (stack.length && indent <= stack[stack.length - 1].indent) {
      stack.pop();
    }
    if (stack.length) {
      stack[stack.length - 1].node.children.push(node);
    } else {
      tree.push(node);
    }
    stack.push({ indent, node });
  }
}

// --- Calculate total weight per node ---
async function calcNode(node) {
  const item = dv.page(node.name);
  node.capacity = Number(item?.Capacity) || null;
  node.selfWeight = await getWeight(node.name, node.count);
  node.childrenWeight = 0;
  for (let child of node.children) {
    await calcNode(child);
    node.childrenWeight += child.totalWeight;
  }
  node.totalWeight = node.selfWeight + node.childrenWeight;
}
await Promise.all(tree.map(calcNode));

// --- Strength Score Calculation ---
const page = dv.page(file.path);
const strengthField = page["Strength"] ?? "0,0";
const [picks, levelUp] = strengthField.split(",").map(n => parseInt(n.trim()));
let racial = 0;

// --- Racial Bonus ---
async function extractLinkFromHeading(headingLabel) {
  const start = lines.findIndex(line => line.trim() === headingLabel);
  if (start === -1) return null;

  for (let i = start + 1; i < lines.length; i++) {
    const line = lines[i].trim();
    if (line.startsWith("#") || line === "") break;
    const match = line.match(/\[\[([^\]]+)\]\]/);
    if (match) return match[1];
  }
  return null;
}

const raceNote = await extractLinkFromHeading("### **Race**");

if (raceNote) {
  const raceFile = app.vault.getMarkdownFiles().find(f => f.basename === raceNote);
  if (raceFile) {
    const raceContent = await app.vault.read(raceFile);
    const raceLines = raceContent.split("\n");
    const abilityStart = raceLines.findIndex(line => line.trim() === "### **Ability**");
    if (abilityStart !== -1) {
      for (let i = abilityStart + 1; i < raceLines.length; i++) {
        const line = raceLines[i].trim();
        if (line.startsWith("#") || line === "") break;
        const match = line.match(/\+(\d+)\s+\[\[Strength\]\]/);
        if (match) {
          racial = parseInt(match[1]);
          break;
        }
      }
    }
  }
}

const strengthScore = 8 + picks + levelUp + racial;

//Race Size
let sizeMultiplier = 15; // default (Medium)
const sizeMap = {
  "Tiny": 7.5,
  "Small": 15,
  "Medium": 15,
  "Large": 30,
  "Huge": 60,
  "Gargantuan": 120
};

if (raceNote) {
  const raceFile = app.vault.getMarkdownFiles().find(f => f.basename === raceNote);
  if (raceFile) {
    const raceContent = await app.vault.read(raceFile);
    const raceLines = raceContent.split("\n");
    const sizeStart = raceLines.findIndex(line => line.trim() === "### **Size**");

    if (sizeStart !== -1) {
      for (let i = sizeStart + 1; i < raceLines.length; i++) {
        const line = raceLines[i].trim();
        if (line.startsWith("#") || line === "") break;

        const match = line.match(/\[\[([^\]]+)\]\]/) || line.match(/(Tiny|Small|Medium|Large|Huge|Gargantuan)/i);
        const size = match ? (match[1] ?? match[0]).trim() : null;

        if (size && sizeMap[size]) {
          sizeMultiplier = sizeMap[size];
        }
      }
    }
  }
}

const maxCarry = strengthScore * sizeMultiplier;

// --- Render containers with overflow check ---
function renderContainers(nodes) {
  for (let node of nodes) {
    if (node.children.length > 0) {
      const cap = node.capacity ? ` / ${node.capacity} lb` : "";
      const nameIsPage = dv.page(node.name) !== undefined;
      const displayName = nameIsPage ? `[[${node.name}]]` : node.name;

      const overCap = node.capacity && node.childrenWeight > node.capacity;
      const weightText = `${node.childrenWeight.toFixed(2)}${cap}`;
      const weightColored = overCap ? `<span style="color:red">${weightText}</span>` : weightText;

      dv.paragraph(`**${displayName}:** ${weightColored}`);
    }
    renderContainers(node.children);
  }
}
renderContainers(tree);

// --- Final output ---
const totalWeight = tree.reduce((sum, node) => sum + node.totalWeight, 0);
const ratio = totalWeight / maxCarry;
const color = ratio > 1 ? "red" : ratio > 2 / 3 ? "orange" : ratio > 1 / 3 ? "yellow" : "green";
dv.paragraph(`**Total Weight:** <span style="color:${color}">${totalWeight.toFixed(2)} / ${Math.floor(maxCarry)} lb</span>`);
```

### **Weapons**
![[Breath Weapon]]

![[Greataxe]]

![[Light Crossbow]]

```dataviewjs
const file = dv.current().file;
const page = dv.page(file.path);
const level = page.level ?? 1;
const files = app.vault.getMarkdownFiles();

// --- Extract Class Link from Character Note ---
const content = await app.vault.read(app.vault.getAbstractFileByPath(file.path));
const lines = content.split("\n");

function extractLinkFromHeading(label) {
  const start = lines.findIndex(l => l.trim() === label);
  if (start === -1) return null;
  for (let i = start + 1; i < lines.length; i++) {
    const ln = lines[i].trim();
    if (ln.startsWith("#") || ln === "") break;
    const m = ln.match(/\[\[([^\]]+)\]\]/);
    if (m) return m[1];
  }
  return null;
}

const className = extractLinkFromHeading("### **Class**");
if (!className) return;

const tableFileName = `${className} Level Table`;
const tableFile = files.find(f => f.basename === tableFileName);
if (!tableFile) return;

// --- Read Frontmatter from Level Table ---
const cache = app.metadataCache.getFileCache(tableFile);
const fm = cache?.frontmatter;
if (!fm) return;

// --- Locate the Spellcasting Array ---
let casting = null;
for (let [key, val] of Object.entries(fm)) {
  if (Array.isArray(val) && val.every(e => e.level !== undefined && e.slots !== undefined)) {
    casting = val;
    break;
  }
}
if (!casting) return;

// --- Match Current Level ---
const entry = casting.find(e => e.level === level);
if (!entry) return;

const noSpellsKnown = entry.spellsKnown == null;
const noCantrips = entry.cantripsKnown == null || entry.cantripsKnown === 0;
const noSpellSlots = !entry.slots || entry.slots.every(s => s === 0);

if (noSpellsKnown && noCantrips && noSpellSlots) return;

// --- Build Header ---
let header = "**Spells**";
if (!noCantrips) {
  header = `**Spells** ([[Cantrip Spell Table|Cantrips Known]]: ${entry.cantripsKnown})`;
}

// --- Get Ordinal ---
function getOrdinal(n) {
  const s = ["th", "st", "nd", "rd"];
  const v = n % 100;
  return n + (s[(v - 20) % 10] || s[v] || s[0]);
}

// --- Build Table (Filtered Columns) ---
const activeLevels = entry.slots
  .map((val, i) => ({ i, val }))
  .filter(e => e.val > 0)
  .map(e => e.i);

const headers = [];
const row = [];

if (!noSpellsKnown) {
  headers.push("Spells Known");
  row.push(entry.spellsKnown);
}

for (let i of activeLevels) {
  headers.push(`[[${getOrdinal(i + 1)} Level Spell Table|Lvl ${i + 1}]]`);
  row.push(entry.slots[i]);
}

// --- Render ---
dv.header(3, header);
dv.table(headers, [row]);
```
### **Features**
##### **Race**
![[Damage Resistance]]

##### **Class**
![[Great Weapon Fighting]]

![[Second Wind]]

##### **Background**
![[City Secrets]]

![[Disguise Kit]]

![[Thieves' Tools]]

### **Race**
![[Dragonborn]]

### **Class**
![[Fighter]]
##### **Selected Skills**
![[Athletics]]
![[Intimidation]]

### **Background**
![[Urchin]]

### **Alignment**
![[Chaotic Neutral]]

### **Characteristics**
##### Gender
- Male
##### [[Draconic Ancestry]]
- Poison
##### Alignment
- Chaotic Neutral
##### Height
- 6’8”
##### Build
- Muscular
##### Scale Color
- Dark green
##### Eyes  
- Blue
##### Notable Features
- dragon head as pictured
##### Clothing  
- Tribal warrior look with fur cloak, armor

### **Backstory**
Draxx never asked for more than a steady job, a roof to sleep under, and a fight now and then to keep the blood warm. Born in the alleys of Duram’s Blackscale District, he clawed his way up from an urchin’s life of grime and hunger to become one of the most feared bouncers in the city’s underbelly. His territory was The Shattered Chalice, a tavern where nobles slummed and criminals dealt beneath flickering lanternlight.

He kept peace with his fists, his glare, and the reputation of his greataxe, Ashrender, which hung behind the bar like a silent threat. But it wasn’t just muscle that earned Draxx respect—he knew who to let in, who to throw out, and who to keep an eye on. The Chalice was neutral ground… until the night King Terra Ny died.

When Maldraxon seized power, the Order of the Celeste raided every known den of dissent. The Shattered Chalice was burned to the ground in the chaos—its patrons executed in the street. Draxx barely escaped with his life after refusing to surrender a rebel courier hiding behind the bar. The Order branded him a traitor for “shielding enemies of the throne.”

Now marked, hunted, and furious, Draxx fled the city. He doesn’t speak of the bodies he stepped over on the way out. What matters is that he’s alive—and so is Ashrender.

From the port of Ankh Marrah, he watches ships leave, always scanning the crowd for a familiar face, or a sign of Princess Aleena. Maybe she’s dead. Maybe not. But Draxx knows one thing:

He was once the gatekeeper of a single tavern. Now, he aims to tear down the doors of a kingdom.