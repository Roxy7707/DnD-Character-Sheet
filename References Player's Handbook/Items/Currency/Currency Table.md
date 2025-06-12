```dataviewjs
const currency = [
  { coin: "**Copper**", cp: "1", sp: "1/10", ep: "1/50", gp: "1/100", pp: "1/1000" },
  { coin: "**Silver**", cp: "10", sp: "1", ep: "1/5", gp: "1/10", pp: "1/100" },
  { coin: "**Electrum**", cp: "50", sp: "5", ep: "1", gp: "1/2", pp: "1/20" },
  { coin: "**Gold**", cp: "100", sp: "10", ep: "2", gp: "1", pp: "1/10" },
  { coin: "**Platinum**", cp: "1000", sp: "100", ep: "50", gp: "10", pp: "1" }
];

dv.table(
  ["Coin", "Cp", "Sp", "Ep", "Gp", "Pp"],
  currency.map(d => [d.coin, d.cp, d.sp, d.ep, d.gp, d.pp])
);
```