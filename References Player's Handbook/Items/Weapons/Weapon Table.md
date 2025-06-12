

```dataviewjs
function convertCost(value, assumedUnit = "gp") {
    if (value == null || isNaN(value)) return "N/A";

    // Convert to copper (cp) for processing
    let valueInCp = value;
    if (assumedUnit === "gp") valueInCp *= 100;
    else if (assumedUnit === "sp") valueInCp *= 10;
    // cp stays as-is

    // Convert to the highest whole-number currency unit
    if (valueInCp % 100 === 0) return `${valueInCp / 100} [[gp]]`;
    else if (valueInCp % 10 === 0) return `${valueInCp / 10} [[sp]]`;
    else return `${valueInCp} [[cp]]`;
}

const weaponTables = [
    { title: "Simple Melee Weapons", path: "References Player's Handbook/Items/Weapons/Simple Melee Weapons" },
    { title: "Simple Ranged Weapons", path: "References Player's Handbook/Items/Weapons/Simple Ranged Weapons" },
    { title: "Martial Melee Weapons", path: "References Player's Handbook/Items/Weapons/Martial Melee Weapons" },
    { title: "Martial Ranged Weapons", path: "References Player's Handbook/Items/Weapons/Martial Ranged Weapons" },
];

for (let table of weaponTables) {
    dv.paragraph(`# **${table.title}**`);
    dv.table(
        ["Category", "Cost", "Damage", "Weight", "Properties", "Range"],
        dv.pages(`"${table.path}"`).map(p => [
            p.file.link,
            convertCost(p.Cost),
            p.Damage,
            p.Weight + " lb",
            p.Properties?.join(", "),
            p.Range
        ])
    );
}

dv.paragraph("# **Ammunition**");
dv.table(
    ["Category", "Cost", "Weight"],
    dv.pages(`"References Player's Handbook/Items/Weapons/Ammo"`).map(p => [
        p.file.link + ` (${p.Quantity})`,
        convertCost(p.Cost),
        p.Weight + " lb"
    ])
);

```
