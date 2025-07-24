---
{"publish":true,"created":"2025-07-24T20:07","modified":"2025-07-24T20:14","published":"2025-07-24T20:22:17.150-03:00","tags":["notas","Log","Quartz"],"cssclasses":""}
---

# Log: Removi title, recent notes e breadcrumb

Removi o Title do quartz. Coloquei um `display: none` no css.
Isso porque costumo usar um H1 como título dentro dos meus arquivos markdown. Então duplicava na renderização do quartz. Triplicava se considerar que também aparecia no breadcrumb.

Também removi o breadcrumb, o explore ao lado já tá fazendo sua função e o tanto de informação estava me irritando.

Não gosto do explore, mas por enquanto ele permanece. Removi o recent notes para não foder ainda mais com a visualização. E até porque é mais interessante estar sincronizado com um controle interno no Obsidian.
