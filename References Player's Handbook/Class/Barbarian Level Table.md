```dataviewjs
const rangerLevels = [
  { level: "1st", bonus: "+2", features: "[[Rage]], [[Unarmored Defense]]", rages: "2", rageDmg: "+2" },
  { level: "2nd", bonus: "+2", features: "[[Reckless Attack]], [[Danger Sense]]", rages: "2", rageDmg: "+2" },
  { level: "3rd", bonus: "+2", features: "[[Primal Path]], _[[Primal Knowledge]] (Optional)_", rages: "3", rageDmg: "+2" },
  { level: "4th", bonus: "+2", features: "Ability Score Improvement", rages: "3", rageDmg: "+2" },
  { level: "5th", bonus: "+3", features: "Extra Attack, [[Fast Movement]]", rages: "3", rageDmg: "+2" },
  { level: "6th", bonus: "+3", features: "[[Path]] feature", rages: "4", rageDmg: "+2" },
  { level: "7th", bonus: "+3", features: "[[Feral Instinct]], _[[Instinctive Pounce]] (Optional)_", rages: "4", rageDmg: "+2" },
  { level: "8th", bonus: "+3", features: "Ability Score Improvement", rages: "4", rageDmg: "+2" },
  { level: "9th", bonus: "+4", features: "[[Brutal Critical]] (1 die)", rages: "4", rageDmg: "+3" },
  { level: "10th", bonus: "+4", features: "[[Path]] feature, _[[Primal Knowledge]] (Optional)_", rages: "4", rageDmg: "+3" },
  { level: "11th", bonus: "+4", features: "[[Relentless Rage]]", rages: "4", rageDmg: "+3" },
  { level: "12th", bonus: "+4", features: "Ability Score Improvement", rages: "5", rageDmg: "+3" },
  { level: "13th", bonus: "+5", features: "[[Brutal Critical]] (2 dice)", rages: "5", rageDmg: "+3" },
  { level: "14th", bonus: "+5", features: "[[Path]] feature", rages: "5", rageDmg: "+3" },
  { level: "15th", bonus: "+5", features: "[[Persistent Rage]]", rages: "5", rageDmg: "+3" },
  { level: "16th", bonus: "+5", features: "Ability Score Improvement", rages: "5", rageDmg: "+4" },
  { level: "17th", bonus: "+6", features: "[[Brutal Critical]] (3 dice)", rages: "6", rageDmg: "+4" },
  { level: "18th", bonus: "+6", features: "[[Indomitable Might]]", rages: "6", rageDmg: "+4" },
  { level: "19th", bonus: "+6", features: "Ability Score Improvement", rages: "6", rageDmg: "+4" },
  { level: "20th", bonus: "+6", features: "[[Primal Champion]]", rages: "∞", rageDmg: "+4" },
];

dv.table(
  ["Level", "[[Proficiency|Proficiency Bonus]]", "Features", "[[Rage|Rages]]", "Rage Damage"],
  rangerLevels.map(r => [r.level, r.bonus, r.features, r.rages, r.rageDmg])
);
```