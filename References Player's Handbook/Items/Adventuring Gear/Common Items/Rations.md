---
Cost: 0.5
Weight: 2
---
Rations consist of dry foods suitable for extended travel, including jerky, dried fruit, hardtack, and nuts.
```dataviewjs
const page = dv.current();

function convertCost(value, assumedUnit = "gp") {
    if (value == null || isNaN(value)) return "N/A";

    let valueInCp = value;
    if (assumedUnit === "gp") valueInCp *= 100;
    else if (assumedUnit === "sp") valueInCp *= 10;

    if (valueInCp % 100 === 0) return `${valueInCp / 100} [[gp]]`;
    else if (valueInCp % 10 === 0) return `${valueInCp / 10} [[sp]]`;
    else return `${valueInCp} [[cp]]`;
}

function formatValue(value) {
    if (value == null) return "-";

    if (Array.isArray(value)) {
        return value.map(formatValue).join(", ");
    }

    if (typeof value === "object" && value.path) {
        const name = value.path.split("/").pop().replace(/\.md$/, "");
        return `[[${name}]]`;
    }

    if (typeof value === "object") {
        return JSON.stringify(value);
    }

    return value;
}

const seenKeys = new Set();
const fields = Object.entries(page)
    .filter(([key]) => !key.startsWith("file"))
    .filter(([key]) => {
        const lowerKey = key.toLowerCase();
        if (seenKeys.has(lowerKey)) return false;
        seenKeys.add(lowerKey);
        return true;
    })
    .map(([key, value]) => {
        let displayValue;
        if (key.toLowerCase() === "cost") {
            displayValue = convertCost(value, "gp");
        } else if (["weight", "capacity"].includes(key.toLowerCase())) {
            displayValue = `${formatValue(value)} lb`;
        } else {
            displayValue = formatValue(value);
        }

        return [key, displayValue];
    });

dv.table(["Field", "Value"], fields);

```
