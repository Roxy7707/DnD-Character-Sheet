
```dataviewjs
dv.table(
    ["Category", "School", "Casting Time", "Range", "Duration", "Components"],
    dv.pages(`"References Player's Handbook/Spells"`)
        .where(p => p.spelllevel && p.spelllevel.path === dv.current().file.path)
        .sort(p => p.file.name, 'asc')
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
                p.file.link,
                p.school,
                p["CastingTime"],
                p.range,
                p.duration,
                componentsDisplay
            ];
        })
);
```
