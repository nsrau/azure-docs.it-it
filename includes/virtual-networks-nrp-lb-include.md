## Bilanciamento del carico
Un bilanciamento del carico viene usato per ridimensionare le applicazioni. Gli scenari di distribuzione più comuni riguardano applicazioni in esecuzione in più istanze della macchina virtuale. Le istanze della macchina virtuale sono gestite dal bilanciamento del carico che consente di distribuire il traffico di rete alle varie istanze.

![Scheda di interfaccia di rete in una macchina virtuale singola](./media/resource-groups-networking/Figure5.png)

I servizi di bilanciamento del carico contengono le risorse figlio seguenti:

- **Configurazione IP front-end**: un bilanciamento del carico può includere uno o più indirizzi IP front-end, anche noti come IP virtuali (VIP). Questi indirizzi IP vengono usati come ingresso per il traffico. 
- **Pool di indirizzi back-end**: indirizzi IP associati alle schede di interfaccia di rete della macchina virtuale a cui viene distribuito il carico.
- **Regole di bilanciamento del carico**: una proprietà della regola esegue il mapping di una specifica combinazione di IP e porte front-end a un set di combinazioni di indirizzi IP e porte back-end. Con una singola definizione di una risorsa di bilanciamento del carico è possibile definire più regole di bilanciamento carico, ciascuna delle quali riflette una combinazione di IP e porte front-end e di IP e porte back-end associata alle macchine virtuali. 
- **Probe**: le probe consentono di tenere traccia dell'integrità delle istanze della macchina virtuale. Se la probe di integrità non riesce, l'istanza della macchina virtuale viene esclusa automaticamente dalla rotazione.
- **Regole NAT in ingresso**: regole NAT che definiscono il traffico in ingresso che attraversa l'IP front-end e viene distribuito all'IP back-end.

<!---HONumber=Oct15_HO3-->