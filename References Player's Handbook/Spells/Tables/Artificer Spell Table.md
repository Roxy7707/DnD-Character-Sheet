
```dataviewjs
dv.table(
    ["Spell Name", "School", "Casting Time", "Range", "Duration", "Components"],
    dv.pages('"References/Spells"')
        .where(p => p.spellclass && p.spellclass.some(c => c.path === dv.current().file.path))
        .sort(p => p.file.name, 'asc')
        .map(p => {
            const links = (p.components || []).filter(c => typeof c === 'object' && c.path);
            const extras = (p.components || []).filter(c => typeof c !== 'object' || !c.path);

            const linkedComponents = links.map(c => c.toString()).join(", ");
            const tooltip = extras.length > 0 
                ? `<span title="${extras.join(", ")}">${linkedComponents}</span>` 
                : linkedComponents;

            return [
                p.file.link,
                p.school,
                p["CastingTime"],
                p.range,
                p.duration,
                tooltip
            ];
        })
);
```
