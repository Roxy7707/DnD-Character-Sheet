---
rangerSpellcasting:
  - level: 1
    spellsKnown: null
    slots: [0, 0, 0, 0, 0]
  - level: 2
    spellsKnown: 2
    slots: [2, 0, 0, 0, 0]
  - level: 3
    spellsKnown: 3
    slots: [3, 0, 0, 0, 0]
  - level: 4
    spellsKnown: 3
    slots: [3, 0, 0, 0, 0]
  - level: 5
    spellsKnown: 4
    slots: [4, 2, 0, 0, 0]
  - level: 6
    spellsKnown: 4
    slots: [4, 2, 0, 0, 0]
  - level: 7
    spellsKnown: 5
    slots: [4, 3, 0, 0, 0]
  - level: 8
    spellsKnown: 5
    slots: [4, 3, 0, 0, 0]
  - level: 9
    spellsKnown: 6
    slots: [4, 3, 2, 0, 0]
  - level: 10
    spellsKnown: 6
    slots: [4, 3, 2, 0, 0]
  - level: 11
    spellsKnown: 7
    slots: [4, 3, 3, 0, 0]
  - level: 12
    spellsKnown: 7
    slots: [4, 3, 3, 0, 0]
  - level: 13
    spellsKnown: 8
    slots: [4, 3, 3, 1, 0]
  - level: 14
    spellsKnown: 8
    slots: [4, 3, 3, 1, 0]
  - level: 15
    spellsKnown: 9
    slots: [4, 3, 3, 2, 0]
  - level: 16
    spellsKnown: 9
    slots: [4, 3, 3, 2, 0]
  - level: 17
    spellsKnown: 10
    slots: [4, 3, 3, 3, 1]
  - level: 18
    spellsKnown: 10
    slots: [4, 3, 3, 3, 1]
  - level: 19
    spellsKnown: 11
    slots: [4, 3, 3, 3, 2]
  - level: 20
    spellsKnown: 11
    slots: [4, 3, 3, 3, 2]
---
```dataviewjs
const rangerLevels = [
  { level: "1st", bonus: "+2", features: "[[Favored Enemy]], [[Natural Explorer]], [[Deft Explorer]] (Optional), [[Favored Foe]] (Optional)" },
  { level: "2nd", bonus: "+2", features: "[[Fighting Style]], [[Spellcasting]], [[Spellcasting Focus]] (Optional)" },
  { level: "3rd", bonus: "+2", features: "[[Primeval Awareness]], [[Ranger Conclave]], [[Primal Awareness]] (Optional)" },
  { level: "4th", bonus: "+2", features: "Ability Score Improvement, [[Martial Versatility]] (Optional)" },
  { level: "5th", bonus: "+3", features: "Extra Attack" },
  { level: "6th", bonus: "+3", features: "[[Favored Enemy]] Improvement, [[Natural Explorer]] Improvement, [[Deft Explorer]] Improvement (Optional)" },
  { level: "7th", bonus: "+3", features: "Ranger Conclave feature" },
  { level: "8th", bonus: "+3", features: "Ability Score Improvement, [[Land's Stride]], Martial Versatility (Optional)" },
  { level: "9th", bonus: "+4", features: "" },
  { level: "10th", bonus: "+4", features: "[[Natural Explorer]] Improvement, [[Hide in Plain Sight]], [[Deft Explorer]] Feature (Optional), [[Nature's Veil]] (Optional)" },
  { level: "11th", bonus: "+4", features: "[[Ranger Conclave]] feature" },
  { level: "12th", bonus: "+4", features: "Ability Score Improvement, [[Martial Versatility]] (Optional)" },
  { level: "13th", bonus: "+5", features: "" },
  { level: "14th", bonus: "+5", features: "[[Favored Enemy]] Improvement, [[Vanish]]" },
  { level: "15th", bonus: "+5", features: "[[Ranger Conclave]] feature" },
  { level: "16th", bonus: "+5", features: "Ability Score Improvement, [[Martial Versatility]] (Optional)" },
  { level: "17th", bonus: "+6", features: "" },
  { level: "18th", bonus: "+6", features: "[[Feral Senses]]" },
  { level: "19th", bonus: "+6", features: "Ability Score Improvement, [[Martial Versatility]] (Optional)" },
  { level: "20th", bonus: "+6", features: "[[Foe Slayer]]" },
];

dv.table(
  ["Level", "[[Proficiency|Proficiency Bonus]]", "Features"],
  rangerLevels.map(r => [r.level, r.bonus, r.features])
);
```