You have a keen mind and a mastery of at least the basics of magic. In many of the worlds of D&D, there are two kinds of [[High Elf|High Elves]]. One type (which includes the gray elves and valley elves of Greyhawk, the Silvanesti of Dragonlance, and the sun elves of the Forgotten Realms) is haughty and reclusive, believing themselves to be superior to non-elves and even other elves. The other type (including the [[High Elf|High Elves]] of Greyhawk, the Qualinesti of Dragonlance, and the moon elves of the Forgotten Realms) are more common and more friendly, and often encountered among humans and other races.
The sun elves of Faerûn (also called gold elves or sunrise elves) have bronze skin and hair of copper, black, or golden blond. Their eyes are golden, silver, or black. Moon elves (also called silver elves or gray elves) are much paler, with alabaster skin sometimes tinged with blue. They often have hair of silver-white, black, or blue, but various shades of blond, brown, and red are not uncommon. Their eyes are blue or green and flecked with gold.

### **Ability**
+2 [[Dexterity]]
+1 [[Intelligence]]

### **Skill**
![[Keen Sences]]
![[Fey Ancestry]]
![[Trance]]

### **Combat**
![[Elf Weapon Training]]

### Spells
1x [[Wizard Spell Table|Wizard]] [[Cantrip Spell Table|Cantrip]]
```dataviewjs
dv.table(
    ["Spell Name", "School", "Casting Time", "Range", "Duration", "Components"],
    dv.pages('"References/Spells"')
        .where(p =>
            p.spellclass &&
            p.spellclass.some(c => c.path === "References/Spells/Tables/Wizard Spell Table.md") &&
            p.spelllevel &&
            p.spelllevel.path === "References/Spells/Tables/Cantrip Spell Table.md"
        )
        .sort(p => p["SpellName"].toString(), 'asc')
        .map(p => {
            const components = p.components || [];

            const linkedComponents = components
                .filter(c => typeof c === 'object' && c.path)
                .map(c => c.toString())
                .join(", ");

            const extras = components
                .filter(c => typeof c !== 'object' || !c.path)
                .join(", ");

            const componentsDisplay = extras
                ? `<span title="${extras}">${linkedComponents}</span>`
                : linkedComponents;

            return [
                p["SpellName"],
                p.school,
                p["CastingTime"],
                p.range,
                p.duration,
                componentsDisplay
            ];
        })
);
```

### **Movement**
Base walking speed of 30 feet
![[Darkvision]]

### **Size**
Small

### **Language**
[[Common]]
[[Elven]]
[[Extra Language]]