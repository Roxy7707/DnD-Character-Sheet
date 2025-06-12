---
Weight: 1
Dexterity: "[[Disadvantage]]"
---

```dataviewjs
const page = dv.current();

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

dv.table(
    ["Field", "Value"],
    Object.entries({
        Weight: page.Weight ? `${page.Weight} lb` : "-",
		Dexterity: page.Dexterity
    })
);
```
