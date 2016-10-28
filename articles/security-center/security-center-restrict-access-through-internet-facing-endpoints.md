<properties
   pageTitle="Limitare l'accesso tramite endpoint con connessione Internet in Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento illustra come implementare la raccomandazione **Restrict access through Internet facing endpoint** (Limita accesso tramite endpoint con connessione Internet) del Centro sicurezza di Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/26/2016"
   ms.author="terrylan"/>

# Limitare l'accesso tramite endpoint con connessione Internet in Centro sicurezza di Azure

Il Centro sicurezza di Azure consiglia di limitare l'accesso tramite endpoint con connessione Internet se uno dei gruppi di sicurezza di rete contiene una o più regole in ingresso che consentono l'accesso da "qualsiasi" indirizzo IP di origine. L'accesso su "qualsiasi" origine potrebbe abilitare utenti malintenzionati ad accedere alle risorse. Il Centro sicurezza consiglierà di modificare queste regole in ingresso per limitare l'accesso agli indirizzi IP di origine che necessitano effettivamente dell'accesso.

Questa raccomandazione viene generata per qualsiasi porta Web con "any" come origine.

> [AZURE.NOTE] Il documento introduce il servizio usando una distribuzione di esempio. Questa non è una guida dettagliata.

## Implementare la raccomandazione

1. Nel pannello **Raccomandazioni** selezionare **Restrict access through Internet facing endpoint** (Limita l'accesso tramite endpoint con connessione Internet). ![Restrict access through Internet facing endpoint (Limita accesso tramite endpoint con connessione Internet)][1]

2. Verrà visualizzato il pannello **Restrict access through Internet facing endpoint** (Limita accesso tramite endpoint con connessione Internet). Questo pannello elenca le macchine virtuali (VM) con regole in ingresso che creano un potenziale problema di sicurezza. Selezionare una macchina virtuale. ![Selezionare una macchina virtuale][2]

3. Il pannello **Gruppo di sicurezza di rete** visualizza le informazioni sul gruppo di sicurezza di rete, le regole in ingresso correlate e la VM associata. Selezionare **Modifica le regole in ingresso** per procedere con la modifica di una regola in ingresso. ![Pannello Gruppo di sicurezza di rete][3]

4. Nel pannello **Regole di sicurezza in ingresso** selezionare la regola in ingresso da modificare. In questo esempio selezioniamo **AllowWeb** (Consenti Web). ![Regole di sicurezza in ingresso][4]

  Si noti che è possibile anche selezionare **Regole predefinite** per visualizzare il set di regole predefinite contenute in tutti i gruppi di sicurezza di rete. Le regole predefinite non possono essere eliminate, ma poiché hanno la priorità più bassa, è possibile eseguirne l'override con le regole create dall'utente. Altre informazioni su [regole predefinite](../virtual-network/ virtual-networks-nsg.md#default-rules). ![Regole predefinite][5]

5. Nel pannello **AllowWeb** (Consenti Web) modificare le proprietà della regola in ingresso in modo che **Origine** sia un indirizzo IP o un blocco di indirizzi IP. Per altre informazioni sulle proprietà della regola in ingresso, vedere [Regole NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).

  ![Modificare la regola in ingresso][6]

## Vedere anche

Questo articolo illustra come implementare la raccomandazione "Restrict access through Internet facing endpoint" (Limita accesso tramite endpoint con connessione Internet) del Centro sicurezza. Per altre informazioni sull'abilitazione dei gruppi di sicurezza di rete e delle regole, vedere gli articoli seguenti:

- [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)
- [Come gestire gruppi di sicurezza di rete tramite il portale di Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare lo stato di integrità delle soluzioni dei partner.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png

<!---HONumber=AcomDC_0727_2016-->