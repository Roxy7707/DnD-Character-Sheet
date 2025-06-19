---
Spellcasting:
  - level: 1
    spellsKnown: 4
    cantripsKnown: 2
    slots: [2, 0, 0, 0, 0, 0, 0, 0, 0]
  - level: 2
    spellsKnown: 5
    cantripsKnown: 2
    slots: [3, 0, 0, 0, 0, 0, 0, 0, 0]
  - level: 3
    spellsKnown: 6
    cantripsKnown: 2
    slots: [4, 2, 0, 0, 0, 0, 0, 0, 0]
  - level: 4
    spellsKnown: 7
    cantripsKnown: 3
    slots: [4, 3, 0, 0, 0, 0, 0, 0, 0]
  - level: 5
    spellsKnown: 8
    cantripsKnown: 3
    slots: [4, 3, 2, 0, 0, 0, 0, 0, 0]
  - level: 6
    spellsKnown: 9
    cantripsKnown: 3
    slots: [4, 3, 3, 0, 0, 0, 0, 0, 0]
  - level: 7
    spellsKnown: 10
    cantripsKnown: 3
    slots: [4, 3, 3, 1, 0, 0, 0, 0, 0]
  - level: 8
    spellsKnown: 11
    cantripsKnown: 3
    slots: [4, 3, 3, 2, 0, 0, 0, 0, 0]
  - level: 9
    spellsKnown: 12
    cantripsKnown: 3
    slots: [4, 3, 3, 3, 1, 0, 0, 0, 0]
  - level: 10
    spellsKnown: 14
    cantripsKnown: 4
    slots: [4, 3, 3, 3, 2, 0, 0, 0, 0]
  - level: 11
    spellsKnown: 15
    cantripsKnown: 4
    slots: [4, 3, 3, 3, 2, 1, 0, 0, 0]
  - level: 12
    spellsKnown: 15
    cantripsKnown: 4
    slots: [4, 3, 3, 3, 2, 1, 0, 0, 0]
  - level: 13
    spellsKnown: 16
    cantripsKnown: 4
    slots: [4, 3, 3, 3, 2, 1, 1, 0, 0]
  - level: 14
    spellsKnown: 18
    cantripsKnown: 4
    slots: [4, 3, 3, 3, 2, 1, 1, 0, 0]
  - level: 15
    spellsKnown: 19
    cantripsKnown: 6
    slots: [4, 3, 3, 3, 2, 1, 1, 1, 0]
  - level: 16
    spellsKnown: 19
    cantripsKnown: 4
    slots: [4, 3, 3, 3, 2, 1, 1, 1, 0]
  - level: 17
    spellsKnown: 20
    cantripsKnown: 4
    slots: [4, 3, 3, 3, 2, 1, 1, 1, 1]
  - level: 18
    spellsKnown: 22
    cantripsKnown: 4
    slots: [4, 3, 3, 3, 3, 1, 1, 1, 1]
  - level: 19
    spellsKnown: 22
    cantripsKnown: 4
    slots: [4, 3, 3, 3, 3, 2, 1, 1, 1]
  - level: 20
    spellsKnown: 22
    cantripsKnown: 4
    slots: [4, 3, 3, 3, 3, 2, 2, 1, 1]
---
```dataviewjs
const rangerLevels = [
  { level: "1st", bonus: "+2", features: "[[Spellcasting]], [[Bardic Inspiration]] ([[d6]])" },
  { level: "2nd", bonus: "+2", features: "[[Jack of All Trades]], [[Song of Rest]] ([[d6]]), _[[Magical Inspiration]] (Optional)_" },
  { level: "3rd", bonus: "+2", features: "[[Bard College]], [[Expertise]]" },
  { level: "4th", bonus: "+2", features: "Ability Score Improvement, _[[Bardic Versatility]] (Optional)_" },
  { level: "5th", bonus: "+3", features: "[[Bardic Inspiration]] ([[d8]]), [[Font of Inspiration]]" },
  { level: "6th", bonus: "+3", features: "[[Countercharm]], [[Bard College]] feature" },
  { level: "7th", bonus: "+3", features: "" },
  { level: "8th", bonus: "+3", features: "Ability Score Improvement, _[[Bardic Versatility]] (Optional)_" },
  { level: "9th", bonus: "+4", features: "[[Song of Rest]] ([[d8]])" },
  { level: "10th", bonus: "+4", features: "[[Bardic Inspiration]] ([[d10]]), [[Expertise]], [[Magical Secrets]]" },
  { level: "11th", bonus: "+4", features: "" },
  { level: "12th", bonus: "+4", features: "Ability Score Improvement, _[[Bardic Versatility]] (Optional)_" },
  { level: "13th", bonus: "+5", features: "[[Song of Rest]] ([[d10]])" },
  { level: "14th", bonus: "+5", features: "[[Magical Secrets]], [[Bard College]] feature" },
  { level: "15th", bonus: "+5", features: "[[Bardic Inspiration]] ([[d12]])" },
  { level: "16th", bonus: "+5", features: "Ability Score Improvement, _[[Bardic Versatility]] (Optional)_" },
  { level: "17th", bonus: "+6", features: "[[Song of Rest]] ([[d12]])" },
  { level: "18th", bonus: "+6", features: "[[Magical Secrets]]" },
  { level: "19th", bonus: "+6", features: "Ability Score Improvement, _[[Bardic Versatility]] (Optional)_" },
  { level: "20th", bonus: "+6", features: "[[Superior Inspiration]]" },
];

dv.table(
  ["Level", "[[Proficiency|Proficiency Bonus]]", "Features"],
  rangerLevels.map(r => [r.level, r.bonus, r.features])
);
```