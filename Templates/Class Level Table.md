---
Spellcasting:
  - level: 1
    spellsKnown: 2
    cantripsKnown: 4
    slots: [2, 0, 0, 0, 0, 0, 0, 0, 0]
  - level: 2
    spellsKnown: 3
    cantripsKnown: 4
    slots: [3, 0, 0, 0, 0, 0, 0, 0, 0]
  - level: 3
    spellsKnown: 4
    cantripsKnown: 4
    slots: [4, 2, 0, 0, 0, 0, 0, 0, 0]
  - level: 4
    spellsKnown: 5
    cantripsKnown: 5
    slots: [4, 3, 0, 0, 0, 0, 0, 0, 0]
  - level: 5
    spellsKnown: 6
    cantripsKnown: 5
    slots: [4, 3, 2, 0, 0, 0, 0, 0, 0]
  - level: 6
    spellsKnown: 7
    cantripsKnown: 5
    slots: [4, 3, 3, 0, 0, 0, 0, 0, 0]
  - level: 7
    spellsKnown: 8
    cantripsKnown: 5
    slots: [4, 3, 3, 1, 0, 0, 0, 0, 0]
  - level: 8
    spellsKnown: 9
    cantripsKnown: 5
    slots: [4, 3, 3, 2, 0, 0, 0, 0, 0]
  - level: 9
    spellsKnown: 10
    cantripsKnown: 5
    slots: [4, 3, 3, 3, 1, 0, 0, 0, 0]
  - level: 10
    spellsKnown: 11
    cantripsKnown: 6
    slots: [4, 3, 3, 3, 2, 0, 0, 0, 0]
  - level: 11
    spellsKnown: 12
    cantripsKnown: 6
    slots: [4, 3, 3, 3, 2, 1, 0, 0, 0]
  - level: 12
    spellsKnown: 12
    cantripsKnown: 6
    slots: [4, 3, 3, 3, 2, 1, 0, 0, 0]
  - level: 13
    spellsKnown: 13
    cantripsKnown: 6
    slots: [4, 3, 3, 3, 2, 1, 1, 0, 0]
  - level: 14
    spellsKnown: 13
    cantripsKnown: 6
    slots: [4, 3, 3, 3, 2, 1, 1, 0, 0]
  - level: 15
    spellsKnown: 14
    cantripsKnown: 6
    slots: [4, 3, 3, 3, 2, 1, 1, 1, 0]
  - level: 16
    spellsKnown: 14
    cantripsKnown: 6
    slots: [4, 3, 3, 3, 2, 1, 1, 1, 0]
  - level: 17
    spellsKnown: 15
    cantripsKnown: 6
    slots: [4, 3, 3, 3, 2, 1, 1, 1, 1]
  - level: 18
    spellsKnown: 15
    cantripsKnown: 6
    slots: [4, 3, 3, 3, 2, 1, 1, 1, 1]
  - level: 19
    spellsKnown: 15
    cantripsKnown: 6
    slots: [4, 3, 3, 3, 2, 1, 1, 1, 1]
  - level: 20
    spellsKnown: 15
    cantripsKnown: 6
    slots: [4, 3, 3, 3, 2, 1, 1, 1, 1]
---
```dataviewjs
const rangerLevels = [
  { level: "1st", bonus: "+2", features: "Blank" },
  { level: "2nd", bonus: "+2", features: "Blank" },
  { level: "3rd", bonus: "+2", features: "Blank" },
  { level: "4th", bonus: "+2", features: "Blank" },
  { level: "5th", bonus: "+3", features: "Blank" },
  { level: "6th", bonus: "+3", features: "Blank" },
  { level: "7th", bonus: "+3", features: "Blank" },
  { level: "8th", bonus: "+3", features: "Blank" },
  { level: "9th", bonus: "+4", features: "Blank" },
  { level: "10th", bonus: "+4", features: "Blank" },
  { level: "11th", bonus: "+4", features: "Blank" },
  { level: "12th", bonus: "+4", features: "Blank" },
  { level: "13th", bonus: "+5", features: "Blank" },
  { level: "14th", bonus: "+5", features: "Blank" },
  { level: "15th", bonus: "+5", features: "Blank" },
  { level: "16th", bonus: "+5", features: "Blank" },
  { level: "17th", bonus: "+6", features: "Blank" },
  { level: "18th", bonus: "+6", features: "Blank" },
  { level: "19th", bonus: "+6", features: "Blank" },
  { level: "20th", bonus: "+6", features: "Blank" },
];

dv.table(
  ["Level", "[[Proficiency|Proficiency Bonus]]", "Features"],
  rangerLevels.map(r => [r.level, r.bonus, r.features])
);
```