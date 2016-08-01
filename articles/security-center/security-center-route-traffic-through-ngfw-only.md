<properties
   pageTitle="Indirizzare il traffico solo tramite firewall di nuova generazione nel Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento illustra come implementare la raccomandazione **Route traffic through NGFW only** (Indirizza il traffico solo tramite il firewall di nuova generazione) del Centro sicurezza di Azure."
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
   ms.date="07/20/2016"
   ms.author="terrylan"/>

# Indirizzare il traffico solo tramite il firewall di nuova generazione nel Centro sicurezza di Azure

Centro sicurezza di Azure rileva quando è stato distribuito un firewall di nuova generazione. Se si dispone di endpoint con connessione Internet, il Centro sicurezza di Azure consiglierà di configurare le regole dei gruppi di sicurezza di rete che forzano il traffico in ingresso alla macchina virtuale (VM) tramite il firewall di nuova generazione.

> [AZURE.NOTE] Il documento introduce il servizio usando una distribuzione di esempio. Questa non è una guida dettagliata.

## Implementare la raccomandazione

1. Nel pannello **Raccomandazioni** selezionare **Route traffic through NGFW only** (Indirizza il traffico solo tramite il firewall di nuova generazione). ![Route traffic through NGFW only (Indirizza il traffico solo tramite il firewall di nuova generazione)][1]

2. Verrà visualizzato il pannello **Route traffic through NGFW only** (Indirizza il traffico solo tramite il firewall di nuova generazione) in cui sono elencate le macchine virtuali che è possibile instradare il traffico. Selezionare una VM dall'elenco. ![Selezionare una macchina virtuale][2]

3. Viene visualizzato un pannello per la VM selezionata con le regole in ingresso correlate. Una descrizione fornisce ulteriori informazioni sui possibili passaggi successivi. ![Configurare le regole per limitare l'accesso][3]

## Vedere anche

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-route-traffic-through-ngfw/route-traffic-through-ngfw.png
[2]: ./media/security-center-route-traffic-through-ngfw/select-vm.png
[3]: ./media/security-center-route-traffic-through-ngfw/configure-rules-to-limit-access.png

<!---HONumber=AcomDC_0720_2016-->