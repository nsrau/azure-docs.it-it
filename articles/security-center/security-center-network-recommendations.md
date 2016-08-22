<properties
   pageTitle="Protezione della rete nel Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento illustra le raccomandazioni presenti nel Centro sicurezza di Azure che facilitano la protezione della rete di Azure e garantiscano la conformità ai criteri di sicurezza."
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
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# Protezione della rete nel Centro sicurezza di Azure

Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari. Le raccomandazioni sono applicabili ai tipi di risorse di Azure, ovvero macchine virtuali, risorse di rete, SQL e applicazioni.

Questo articolo illustra le raccomandazioni applicabili alla rete. Le raccomandazioni per le risorse di rete sono incentrate sui firewall di nuova generazione, sui gruppi di sicurezza di rete, sulla configurazione delle regole di traffico in ingresso e altro ancora. Usare la tabella seguente come riferimento per conoscere le raccomandazioni disponibili per le risorse di rete e gli effetti che producono se si decide di metterle in pratica.

## Indicazioni disponibili per la rete

|Raccomandazione|Descrizione|
|-----|-----|
|[Aggiungi un firewall di nuova generazione](security-center-add-next-generation-firewall.md)|Il Centro sicurezza di Azure consiglia di aggiungere un firewall di nuova generazione di un partner Microsoft per aumentare la protezione delle applicazioni Web.|
|[Route traffic through NGFW only (Indirizza il traffico solo tramite il firewall di nuova generazione)](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Consiglia di configurare le regole del gruppo di sicurezza di rete che forzano il traffico in ingresso alla VM tramite il firewall di nuova generazione.|
|[Abilita i gruppi di sicurezza di rete sulle subnet o sulle macchine virtuali](security-center-enable-network-security-groups.md)|Consiglia di attivare i gruppo di sicurezza di rete sulle subnet o sulle macchine virtuali.|
|[Restrict access through Internet facing endpoint (Limita l'accesso tramite endpoint con connessione Internet)](security-center-restrict-access-through-internet-facing-endpoints.md)|Consiglia di configurare le regole del traffico in ingresso per i gruppi di sicurezza di rete.|

## Vedere anche

Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

- [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-recommendations.md)
- [Protecting your applications in Azure Security Center](security-center-application-recommendations.md) (Protezione delle applicazioni nel Centro sicurezza di Azure)
- [Protezione del servizio SQL di Azure nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.

<!---HONumber=AcomDC_0810_2016-->