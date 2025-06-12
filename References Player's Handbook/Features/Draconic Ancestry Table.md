```dataviewjs
const dragons = [
  { color: "Black", type: "Acid", breath: "5 by 30 ft. line (DEX save)" },
  { color: "Blue", type: "Lightning", breath: "5 by 30 ft. line (DEX save)" },
  { color: "Brass", type: "Fire", breath: "5 by 30 ft. line (DEX save)" },
  { color: "Bronze", type: "Lightning", breath: "5 by 30 ft. line (DEX save)" },
  { color: "Copper", type: "Acid", breath: "5 by 30 ft. line (DEX save)" },
  { color: "Gold", type: "Fire", breath: "15 ft. cone (DEX save)" },
  { color: "Green", type: "Poison", breath: "15 ft. cone (CON save)" },
  { color: "Red", type: "Fire", breath: "15 ft. cone (DEX save)" },
  { color: "Silver", type: "Cold", breath: "15 ft. cone (CON save)" },
  { color: "White", type: "Cold", breath: "15 ft. cone (CON save)" }
];

dv.table(
  ["Dragon Color", "Damage Type", "Breath Weapon"],
  dragons.map(d => [d.color, d.type, d.breath])
);
```