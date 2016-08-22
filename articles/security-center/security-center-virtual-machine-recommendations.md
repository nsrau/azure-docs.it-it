<properties
   pageTitle="Protezione delle macchine virtuali nel Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento illustra le raccomandazioni presenti nel Centro sicurezza di Azure che facilitano la protezione delle macchine virtuali e garantiscano la conformità ai criteri di sicurezza."
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

# Protezione delle macchine virtuali nel Centro sicurezza di Azure

Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari. Le raccomandazioni sono applicabili ai tipi di risorse di Azure, ovvero macchine virtuali, risorse di rete, SQL e applicazioni.

Questo articolo illustra le raccomandazioni applicabili alle macchine virtuali. Le raccomandazioni sulle macchine virtuali sono incentrate sulla raccolta dei dati, sull'applicazione degli aggiornamenti del sistema, sul provisioning di antimalware, sulla crittografia dei dischi delle macchine virtuali e altro ancora. Usare la tabella seguente come riferimento per conoscere le raccomandazioni disponibili per le macchine virtuali e gli effetti che producono se si decide di metterle in pratica.

## Raccomandazioni disponibili per le macchine virtuali

|Raccomandazione|Descrizione|
|-----|-----|
|[Abilita la raccolta di dati per le sottoscrizioni](security-center-enable-data-collection.md)|Consiglia di attivare la raccolta dati nei criteri di sicurezza per ogni sottoscrizione e per tutte le macchine virtuali nelle sottoscrizioni.|
|[Remediate OS vulnerabilities (Risolvi vulnerabilità del sistema operativo)](security-center-remediate-os-vulnerabilities.md)|Consiglia di allineare le configurazioni dei sistemi operativi alle regole di configurazione raccomandate, ad esempio non consentire il salvataggio delle password.|
|[Applicare gli aggiornamenti di sistema](security-center-apply-system-updates.md)|Consiglia di distribuire gli aggiornamenti critici e della sicurezza di sistema mancanti nelle macchine virtuali.|
|[Riavvia dopo gli aggiornamenti del sistema](security-center-apply-system-updates.md#reboot-after-system-updates)|Consiglia di riavviare una macchina virtuale per completare il processo di applicazione degli aggiornamenti del sistema.|
|[Installa Endpoint Protection](security-center-install-endpoint-protection.md)|Suggerisce di effettuare il provisioning dei programmi antimalware nelle macchine virtuali (solo VM Windows).|
|[Risolvi gli avvisi sull'integrità di Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Si consiglia di correggere gli errori relativi alla protezione degli endpoint.|
|[Abilita l'agente di macchine virtuali](security-center-enable-vm-agent.md)|Consente di identificare le macchine virtuali per le quali è necessario l'agente di macchine virtuali, che deve essere installato nelle macchine virtuali per poter effettuare il provisioning dei programmi di analisi delle patch, analisi della baseline e antimalware. Per impostazione predefinita, l'agente di macchine virtuali è installato nelle macchine virtuali distribuite da Azure Marketplace. L'articolo relativo all'[agente di macchine virtuali e relative estensioni, parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornisce informazioni su come installare l'agente di macchine virtuali.|
| [Applicare Crittografia dischi](security-center-apply-disk-encryption.md) |Suggerisce di crittografare i dischi delle macchine virtuali con Crittografia dischi di Azure (VM Windows e Linux). La crittografia è consigliabile sia per il sistema operativo sia per i volumi di dati della macchina virtuale.|
| [Aggiornare la versione sistema operativo](security-center-update-os-version.md) | Suggerisce di aggiornare la versione del sistema operativo per il servizio cloud alla versione più recente disponibile per la famiglia di sistemi operativi. Per altre informazioni sul servizio cloud, vedere [Perché scegliere Servizi cloud](../cloud-services/cloud-services-choose-me.md). |

## Vedere anche

Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

- [Protecting your applications in Azure Security Center](security-center-application-recommendations.md) (Protezione delle applicazioni nel Centro sicurezza di Azure)
- [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)
- [Protezione del servizio SQL di Azure nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.

<!---HONumber=AcomDC_0810_2016-->