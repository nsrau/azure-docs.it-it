<a name="virtual-networking-limits-classic"></a>I limiti seguenti si applicano solo per le risorse di rete gestite tramite il modello di distribuzione classica per sottoscrizione.

| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Reti virtuali |50 |100 |
| Siti di rete locale |20 |Contattare il supporto tecnico |
| Server DNS per rete virtuale |20 |100 |
| Indirizzi IP privati per rete virtuale |4096 |4096 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500K |500K |
| Gruppi di sicurezza di rete (NGS) |100 |200 |
| Regole NSG per NSG |200 |400 |
| Tabelle di route definite dall’utente |100 |200 |
| Le route definite dall’utente per ogni tabella di route |100 |400 |
| Indirizzi IP pubblici (dinamici) |5 |contattare il supporto tecnico |
| Indirizzi IP pubblici riservati. |20 |Contattare il supporto tecnico |
| VIP pubblico per ogni distribuzione |5 |Contattare il supporto tecnico |
| VIP privato (ILB) per ogni distribuzione |1 |1 |
| Elenchi di controllo di accesso (ACL) per endpoint |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limiti relativi alle reti - Azure Resource Manager
I seguenti limiti si applicano solo per le risorse di rete gestite tramite Azure Resource Manager per area per sottoscrizione.

| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Reti virtuali |50 |1000 |
| Subnet per rete virtuale |1000 |10000 |
| Peering reti virtuali per rete virtuale |10 |50 |
| Server DNS per rete virtuale |9 |25 |
| Indirizzi IP privati per rete virtuale |4096 |8192 |
| Indirizzi IP privati per interfaccia di rete |256 |1024 |
| Flussi TCP o UDP simultanei per ogni scheda di interfaccia di rete di un'istanza del ruolo o di una macchina virtuale |500K |500K |
| Interfacce di rete (NIC) |350 |20000 |
| Gruppi di sicurezza di rete (NGS) |100 |5000 |
| Regole NSG per NSG |200 |500 |
| Indirizzi IP e intervalli specificati per l'origine o la destinazione in una regola di sicurezza |2000 |4000 |
| Gruppi di sicurezza delle applicazioni |200 |500 |
| Gruppi di sicurezza delle applicazioni per ogni configurazione IP, per ogni scheda di interfaccia di rete |10 |20 |
| Configurazioni IP per ogni gruppo di sicurezza delle applicazioni |1000 |4000 |
| Gruppi di sicurezza delle applicazioni che possono essere specificati in tutte le regole di sicurezza di un gruppo di sicurezza di rete |50 |100 |
| Tabelle di route definite dall’utente |100 |200 |
| Le route definite dall’utente per ogni tabella di route |100 |400 |
| Indirizzi IP pubblici: dinamici |(Basic) |60 |Contattare il supporto tecnico |
| Indirizzi IP pubblici: statici |(Basic) 20 |Contattare il supporto tecnico |
| Indirizzi IP pubblici: statici |(Standard) 20 |Contattare il supporto tecnico |
| Servizi di bilanciamento del carico (interni e con connessione Internet, Basic e Standard) |100 |1000 |
| Regole del servizio di bilanciamento del carico (Basic) per ogni servizio di bilanciamento del carico |150 |250 |
| Regole del servizio di bilanciamento del carico (Standard) per ogni servizio di bilanciamento del carico | 1250 | 1500|
| Regole del servizio di bilanciamento del carico (Basic) per ogni configurazione IP |250 |250 |
| IP front-end pubblico per ogni servizio di bilanciamento del carico (Basic) |10 |30 |
| IP front-end pubblico per ogni servizio di bilanciamento del carico (Standard) | 10 | 600 |
| IP front-end privato per ogni servizio di bilanciamento del carico (Basic) |10 |Contattare il supporto tecnico |
| IP front-end privato per ogni servizio di bilanciamento del carico (Standard) | 10 | 600 |
| Certificati radice Point-to-Site per ogni gateway VPN |20 |20 |


Se è necessario aumentare i limiti rispetto all'impostazione predefinita, [contattare il supporto tecnico](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

