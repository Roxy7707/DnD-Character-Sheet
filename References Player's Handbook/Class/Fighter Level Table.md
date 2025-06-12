```dataviewjs
const levels = [
  { level: "1st", bonus: "+2", feature: "[[Fighting Style]], [[Second Wind]]" },
  { level: "2nd", bonus: "+2", feature: "[[Action Surge]] (one use)" },
  { level: "3rd", bonus: "+2", feature: "[[Martial]] Archetype" },
  { level: "4th", bonus: "+2", feature: "Ability Score Improvement" },
  { level: "5th", bonus: "+3", feature: "Extra Attack" },
  { level: "6th", bonus: "+3", feature: "Ability Score Improvement" },
  { level: "7th", bonus: "+3", feature: "[[Martial]] Archetype feature" },
  { level: "8th", bonus: "+3", feature: "Ability Score Improvement" },
  { level: "9th", bonus: "+4", feature: "[[Indomitable]] (one use)" },
  { level: "10th", bonus: "+4", feature: "[[Martial]] Archetype feature" },
  { level: "11th", bonus: "+4", feature: "Extra Attack (2)" },
  { level: "12th", bonus: "+4", feature: "Ability Score Improvement" },
  { level: "13th", bonus: "+5", feature: "[[Indomitable]] (two uses)" },
  { level: "14th", bonus: "+5", feature: "Ability Score Improvement" },
  { level: "15th", bonus: "+5", feature: "[[Martial]] Archetype feature" },
  { level: "16th", bonus: "+5", feature: "Ability Score Improvement" },
  { level: "17th", bonus: "+6", feature: "[[Action Surge]] (two uses), [[Indomitable]] (three uses)" },
  { level: "18th", bonus: "+6", feature: "[[Martial]] Archetype feature" },
  { level: "19th", bonus: "+6", feature: "Ability Score Improvement" },
  { level: "20th", bonus: "+6", feature: "Extra Attack (3)" }
];

dv.table(
  ["Level", "[[Proficiency|Proficiency Bonus]]", "Features"],
  levels.map(l => [l.level, l.bonus, l.feature])
);
```