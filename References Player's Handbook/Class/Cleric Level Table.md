---
Spellcasting:
  - level: 1
    spellsKnown: null
    cantripsKnown: 3
    slots: [2, 0, 0, 0, 0, 0, 0, 0, 0]
  - level: 2
    spellsKnown: null
    cantripsKnown: 3
    slots: [3, 0, 0, 0, 0, 0, 0, 0, 0]
  - level: 3
    spellsKnown: null
    cantripsKnown: 3
    slots: [4, 2, 0, 0, 0, 0, 0, 0, 0]
  - level: 4
    spellsKnown: null
    cantripsKnown: 4
    slots: [4, 3, 0, 0, 0, 0, 0, 0, 0]
  - level: 5
    spellsKnown: null
    cantripsKnown: 4
    slots: [4, 3, 2, 0, 0, 0, 0, 0, 0]
  - level: 6
    spellsKnown: null
    cantripsKnown: 4
    slots: [4, 3, 3, 0, 0, 0, 0, 0, 0]
  - level: 7
    spellsKnown: null
    cantripsKnown: 4
    slots: [4, 3, 3, 1, 0, 0, 0, 0, 0]
  - level: 8
    spellsKnown: null
    cantripsKnown: 4
    slots: [4, 3, 3, 2, 0, 0, 0, 0, 0]
  - level: 9
    spellsKnown: null
    cantripsKnown: 4
    slots: [4, 3, 3, 3, 1, 0, 0, 0, 0]
  - level: 10
    spellsKnown: null
    cantripsKnown: 5
    slots: [4, 3, 3, 3, 2, 0, 0, 0, 0]
  - level: 11
    spellsKnown: null
    cantripsKnown: 5
    slots: [4, 3, 3, 3, 2, 1, 0, 0, 0]
  - level: 12
    spellsKnown: null
    cantripsKnown: 5
    slots: [4, 3, 3, 3, 2, 1, 0, 0, 0]
  - level: 13
    spellsKnown: null
    cantripsKnown: 5
    slots: [4, 3, 3, 3, 2, 1, 1, 0, 0]
  - level: 14
    spellsKnown: null
    cantripsKnown: 5
    slots: [4, 3, 3, 3, 2, 1, 1, 0, 0]
  - level: 15
    spellsKnown: null
    cantripsKnown: 5
    slots: [4, 3, 3, 3, 2, 1, 1, 1, 0]
  - level: 16
    spellsKnown: null
    cantripsKnown: 5
    slots: [4, 3, 3, 3, 2, 1, 1, 1, 0]
  - level: 17
    spellsKnown: null
    cantripsKnown: 5
    slots: [4, 3, 3, 3, 2, 1, 1, 1, 1]
  - level: 18
    spellsKnown: null
    cantripsKnown: 5
    slots: [4, 3, 3, 3, 3, 1, 1, 1, 1]
  - level: 19
    spellsKnown: null
    cantripsKnown: 5
    slots: [4, 3, 3, 3, 3, 2, 1, 1, 1]
  - level: 20
    spellsKnown: null
    cantripsKnown: 5
    slots: [4, 3, 3, 3, 3, 2, 2, 1, 1]
---
```dataviewjs
const rangerLevels = [
  { level: "1st", bonus: "+2", features: "[[Spellcasting]], [[Divine Domain]]" },
  { level: "2nd", bonus: "+2", features: "[[Channel Divinity]] (x1), [[Divine Domain]] feature, _[[Harness Divine Power]] (Optional)_" },
  { level: "3rd", bonus: "+2", features: "" },
  { level: "4th", bonus: "+2", features: "Ability Score Improvement, _[[Cantrip Versatility]] (Optional)_" },
  { level: "5th", bonus: "+3", features: "[[Destroy Undead]] (CR 1/2)" },
  { level: "6th", bonus: "+3", features: "[[Channel Divinity]] (x2), [[Divine Domain]] feature" },
  { level: "7th", bonus: "+3", features: "" },
  { level: "8th", bonus: "+3", features: "Ability Score Improvement, [[Destroy Undead]] (CR 1), [[Divine Domain]] feature, _[[Cantrip Versatility]] (Optional)_" },
  { level: "9th", bonus: "+4", features: "" },
  { level: "10th", bonus: "+4", features: "[[Divine Intervention]]" },
  { level: "11th", bonus: "+4", features: "[[Destroy Undead]] (CR 2)" },
  { level: "12th", bonus: "+4", features: "Ability Score Improvement, _[[Cantrip Versatility]] (Optional)_" },
  { level: "13th", bonus: "+5", features: "" },
  { level: "14th", bonus: "+5", features: "[[Destroy Undead]] (CR 3)" },
  { level: "15th", bonus: "+5", features: "" },
  { level: "16th", bonus: "+5", features: "Ability Score Improvement, _[[Cantrip Versatility]] (Optional)_" },
  { level: "17th", bonus: "+6", features: "[[Destroy Undead]] (CR 4), [[Divine Domain]] feature" },
  { level: "18th", bonus: "+6", features: "[[Channel Divinity]] (x3)" },
  { level: "19th", bonus: "+6", features: "Ability Score Improvement, _[[Cantrip Versatility]] (Optional)_" },
  { level: "20th", bonus: "+6", features: "[[Divine Intervention]] improvement" },
];

dv.table(
  ["Level", "[[Proficiency|Proficiency Bonus]]", "Features"],
  rangerLevels.map(r => [r.level, r.bonus, r.features])
);
```