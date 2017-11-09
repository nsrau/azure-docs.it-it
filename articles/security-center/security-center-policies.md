---
title: Impostare i criteri di sicurezza nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento consente di configurare i criteri di sicurezza nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 1cebb6edecd13c6ab32c6854bfd6fe908c1f71f4
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="set-security-policies-in-security-center"></a>Impostare i criteri di sicurezza nel Centro sicurezza
Questo documento consente di configurare i criteri di sicurezza nel Centro sicurezza mostrando i passaggi necessari per eseguire questa attività. 


## <a name="how-security-policies-work"></a>Funzionamento dei criteri di sicurezza
Il Centro sicurezza crea automaticamente un criterio di sicurezza predefinito per ogni sottoscrizione di Azure. È possibile modificare il criterio nel Centro sicurezza e monitorare la conformità ai criteri. 

> [!NOTE]
> I criteri del Centro sicurezza possono ora essere estesi usando Criteri di Azure, disponibile in anteprima limitata. Fare clic [qui](http://aka.ms/getpolicy) per partecipare all'anteprima oppure vedere la documentazione [qui](security-center-azure-policy.md).

Ad esempio, le risorse usate per lo sviluppo o il test possono avere requisiti di sicurezza diversi da quelli delle risorse usate per le applicazioni di produzione. In modo analogo, le applicazioni che usano dati regolamentati come le informazioni personali possono richiedere un maggiore livello di sicurezza. I criteri di protezione abilitati nel Centro sicurezza di Azure determinano i suggerimenti per la sicurezza e il monitoraggio per identificare le potenziali vulnerabilità e attenuare le minacce. Per altre informazioni su come determinare l'opzione più appropriata, vedere [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md) .

## <a name="edit-security-policies"></a>Modificare i criteri di sicurezza
È possibile modificare i criteri di sicurezza predefiniti per ogni sottoscrizione di Azure nel Centro sicurezza. Per modificare i criteri di sicurezza, è necessario essere proprietario, collaboratore o amministratore della sicurezza della sottoscrizione. Accedere al portale di Azure e seguire questa procedura per configurare i criteri di sicurezza nel Centro sicurezza di Azure: 

1.  Nel dashboard **Centro sicurezza** in **Generale** fare clic su **Criteri di sicurezza**.
2.  Selezionare la sottoscrizione in cui abilitare i criteri di sicurezza.
3.  Nella sezione **COMPONENTI DEI CRITERI** fare clic su **Criteri di sicurezza**.
4.  Questi sono i criteri predefiniti assegnati dal Centro sicurezza. È possibile attivare o disattivare le raccomandazioni sulla sicurezza disponibili.
5.  Al termine delle modifiche, fare clic su **Salva**.

## <a name="available-security-policy-options"></a>Opzioni di criteri di sicurezza disponibili

Usare la tabella seguente come riferimento per comprendere ogni opzione:

| Criteri | Quando lo stato è Sì |
| --- | --- |
| Aggiornamenti del sistema |Recupera un elenco giornaliero degli aggiornamenti della sicurezza e critici da Windows Update o da Windows Server Update Services. L'elenco recuperato dipende dal servizio configurato per tale macchina virtuale e consiglia di applicare gli aggiornamenti mancanti. Per i sistemi Linux, il criterio usa il sistema di gestione pacchetti fornito dalla distribuzione per determinare per quali pacchetti sono disponibili aggiornamenti. Controlla anche la presenza di aggiornamenti critici e della sicurezza dalle macchine virtuali di [Servizi cloud di Azure](../cloud-services/cloud-services-how-to-configure.md). |
| Vulnerabilità del sistema operativo |Analizza giornalmente le configurazioni del sistema operativo per determinare i problemi che potrebbero rendere vulnerabile agli attacchi la macchina virtuale. Il criterio consiglia anche le modifiche alla configurazione necessarie per risolvere queste vulnerabilità. Vedere l' [elenco delle baseline consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) per altre informazioni sulle configurazioni specifiche monitorate. (Al momento, Windows Server 2016 non è completamente supportato.) |
| Endpoint Protection |Suggerisce l'Endpoint Protection di cui eseguire il provisioning per tutte le macchine virtuali di Windows, per identificare e rimuovere virus, spyware e altro software dannoso. |
| Crittografia del disco |Suggerisce di abilitare la crittografia dischi in tutte le macchine virtuali per migliorare la protezione dei dati inattivi. |
| Gruppi di sicurezza di rete |Consiglia di configurare [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) per controllare il traffico in ingresso e in uscita nelle VM che hanno endpoint pubblici. I gruppi di sicurezza di rete configurati per una subnet vengono ereditati da tutte le interfacce di rete della macchina virtuale se non diversamente specificato. Oltre a controllare che sia stato configurato un gruppo di sicurezza di rete, questo criterio valuta le regole di sicurezza in ingresso per identificare le regole che consentono il traffico in ingresso. |
| Web application firewall |Consiglia di effettuare il provisioning di un Web application firewall nelle macchine virtuali quando una delle due affermazioni seguenti è vera: </br></br>Viene usato l'[IP pubblico a livello di istanza](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) e le regole di sicurezza in ingresso per il gruppo di sicurezza di rete associato vengono configurate in modo da consentire l'accesso alla porta 80/443.</br></br>IP con carico bilanciato e le regole NAT (Network Address Translation) in ingresso e di bilanciamento del carico associate sono configurate per consentire l'accesso alla porta 80/443. Per altre informazioni, vedere [Supporto di Azure Resource Manager per il servizio di bilanciamento del carico](../load-balancer/load-balancer-arm.md). |
| Firewall di nuova generazione |Estende le protezioni di rete oltre i gruppi di sicurezza di rete predefiniti di Azure. Il Centro sicurezza troverà le distribuzioni per cui è consigliabile un firewall di nuova generazione e consentirà di effettuare il provisioning di un'appliance virtuale. |
| Servizio di controllo SQL e rilevamento delle minacce |Consiglia l'abilitazione del controllo dell'accesso al database di Azure per la conformità e il rilevamento avanzato delle minacce, per scopi di analisi. |
| Crittografia SQL |Consiglia l'abilitazione della crittografia dati inattivi per il database SQL di Azure, i backup associati e file di log delle transazioni. Anche se i dati vengono violati, non saranno leggibili. |
| Valutazione della vulnerabilità |Consiglia di installare una soluzione di valutazione della vulnerabilità nella VM. |
| Crittografia di archiviazione |Questa funzionalità è attualmente disponibile per BLOB di Azure e File di Azure. Dopo l'abilitazione della Crittografia del servizio di archiviazione verranno crittografati solo i nuovi dati, mentre i file già esistenti nell'account di archiviazione rimarranno non crittografati. |
| Accesso alla rete JIT |Quando è abilitata la funzionalità Just-in-Time, Centro sicurezza protegge il traffico in ingresso alle macchine virtuali di Azure creando una regola del gruppo di sicurezza di rete. Selezionare le porte nella macchina virtuale per cui bloccare il traffico in ingresso. Per altre informazioni, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |


## <a name="next-step"></a>Passaggio successivo
In questo documento si è appreso come configurare i criteri di sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md). Informazioni sulla pianificazione e considerazioni di progettazione per l'adozione del Centro sicurezza di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md). Informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md). Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
