---
title: Impostare i criteri di sicurezza nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo articolo illustra come configurare i criteri di sicurezza nel Centro sicurezza di Azure.
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
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: 4d1a30b046c0c398d934291a907af891e9ac7fdf
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="set-security-policies-in-azure-security-center"></a>Impostare i criteri di sicurezza nel Centro sicurezza di Azure
Questo articolo illustra come configurare i criteri di sicurezza nel Centro sicurezza.

## <a name="how-security-policies-work"></a>Funzionamento dei criteri di sicurezza
Il Centro sicurezza crea automaticamente un criterio di sicurezza predefinito per ogni sottoscrizione di Azure. Nel Centro sicurezza è possibile modificare i criteri e monitorarne la conformità.

> [!NOTE]
> I criteri del Centro sicurezza possono ora essere estesi usando Criteri di Azure, disponibile in anteprima limitata. Per partecipare all'anteprima, passare a [Sign up for Azure Policy](https://aka.ms/getpolicy) (Iscrizione a Criteri di Azure). Per altre informazioni, vedere [Integrare i criteri di sicurezza del Centro sicurezza con Criteri di Azure](security-center-azure-policy.md).

I requisiti di sicurezza per le risorse usate per lo sviluppo o i test possono variare rispetto ai requisiti per le risorse usate per le applicazioni di produzione. Le applicazioni che usano dati regolamentati, come le informazioni personali, possono richiedere un livello di sicurezza superiore. I criteri di protezione abilitati nel Centro sicurezza di Azure determinano i suggerimenti per la sicurezza e il monitoraggio per identificare le potenziali vulnerabilità e attenuare le minacce. Per altre informazioni su come determinare l'opzione più appropriata, vedere [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md).

## <a name="edit-security-policies"></a>Modificare i criteri di sicurezza
È possibile modificare i criteri di sicurezza predefiniti per ogni sottoscrizione di Azure nel Centro sicurezza. Per modificare i criteri di sicurezza, è necessario essere proprietario, collaboratore o amministratore della sicurezza della sottoscrizione. Per configurare i criteri di sicurezza nel Centro sicurezza, seguire questa procedura:

1. Accedere al portale di Azure.

2. Nel dashboard **Centro sicurezza** in **Generale** selezionare **Criteri di sicurezza**.

3. Selezionare la sottoscrizione per cui abilitare un criterio di sicurezza.

4. Nella sezione **Componenti dei criteri** selezionare **Criteri di sicurezza**.  
    Questi sono i criteri predefiniti assegnati dal Centro sicurezza. È possibile attivare o disattivare le raccomandazioni sulla sicurezza disponibili.

5. Al termine delle modifiche, selezionare **Salva**.

## <a name="available-security-policy-definitions"></a>Definizioni di criteri di sicurezza disponibili

Per informazioni sulle definizioni dei criteri disponibili nel criterio di sicurezza predefinito, vedere la tabella seguente:

| Criterio | Operazioni eseguite dai criteri |
| --- | --- |
| Aggiornamenti del sistema |Recupera un elenco giornaliero degli aggiornamenti della sicurezza e critici da Windows Update o da Windows Server Update Services. L'elenco recuperato dipende dal servizio configurato per le macchine virtuali e consiglia di applicare gli aggiornamenti mancanti. Per i sistemi Linux, il criterio usa il sistema di gestione pacchetti fornito dalla distribuzione per determinare per quali pacchetti sono disponibili aggiornamenti. Controlla anche la presenza di aggiornamenti critici e della sicurezza dalle macchine virtuali di [Servizi cloud di Azure](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Configurazioni di sicurezza |Analizza giornalmente le configurazioni del sistema operativo per determinare i problemi che potrebbero rendere vulnerabile agli attacchi la macchina virtuale. Il criterio consiglia anche le modifiche alla configurazione necessarie per risolvere queste vulnerabilità. Per altre informazioni sulle configurazioni specifiche sottoposte a monitoraggio, vedere l'[elenco di baseline consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (Al momento, Windows Server 2016 non è completamente supportato.) |
| Endpoint Protection |Consiglia di configurare Endpoint Protection per tutte le macchine virtuali di Windows, per identificare e rimuovere virus, spyware e altro software dannoso. |
| Crittografia del disco |Suggerisce di abilitare la crittografia dischi in tutte le macchine virtuali per migliorare la protezione dei dati inattivi. |
| Gruppi di sicurezza di rete |Consiglia di configurare [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) per controllare il traffico in ingresso e in uscita nelle VM che hanno endpoint pubblici. I gruppi di sicurezza di rete configurati per una subnet vengono ereditati da tutte le interfacce di rete della macchina virtuale se non diversamente specificato. Oltre a controllare che sia stato configurato un gruppo di sicurezza di rete, questo criterio valuta le regole di sicurezza in ingresso per identificare le regole che consentono il traffico in ingresso. |
| Web application firewall |Consiglia di configurare un Web application firewall nelle macchine virtuali quando una delle due affermazioni seguenti è vera: <ul><li>Viene usato un [IP pubblico a livello di istanza](../virtual-network/virtual-networks-instance-level-public-ip.md) e le regole di sicurezza in ingresso per il gruppo di sicurezza di rete associato vengono configurate in modo da consentire l'accesso alla porta 80/443.</li><li>Viene usato un IP con carico bilanciato e le regole NAT (Network Address Translation) in ingresso e di bilanciamento del carico associate sono configurate per consentire l'accesso alla porta 80/443. Per altre informazioni, vedere [Supporto di Azure Resource Manager per Load Balancer](../load-balancer/load-balancer-arm.md).</li> |
| Firewall di nuova generazione |Estende le protezioni di rete oltre i gruppi di sicurezza di rete predefiniti di Azure. Il Centro sicurezza individua le distribuzioni per cui è consigliabile un firewall di nuova generazione ed è quindi possibile configurare un'appliance virtuale. |
| Controllo e rilevamento delle minacce SQL |Consiglia l'abilitazione del controllo dell'accesso al database SQL per la conformità e il rilevamento avanzato delle minacce, per scopi di analisi. |
| Crittografia SQL |Consiglia l'abilitazione della crittografia dei dati inattivi per il database SQL, i backup associati e file di log delle transazioni. Anche se i dati vengono violati, non sono leggibili. |
| Valutazione della vulnerabilità |Consiglia di installare una soluzione di valutazione della vulnerabilità nella VM. |
| Crittografia di archiviazione |Questa funzionalità è attualmente disponibile per BLOB e File di Azure. Dopo l'abilitazione della Crittografia del servizio di archiviazione vengono crittografati solo i nuovi dati, mentre i file già esistenti nell'account di archiviazione rimangono non crittografati. |
| Accesso alla rete JIT |Quando è abilitato l'accesso alla rete Just-in-Time, il Centro sicurezza protegge il traffico in ingresso alle macchine virtuali di Azure creando una regola del gruppo di sicurezza di rete. Selezionare le porte nella macchina virtuale per cui bloccare il traffico in ingresso. Per altre informazioni, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |


## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come configurare i criteri di sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md): informazioni sulla pianificazione e considerazioni di progettazione per il Centro sicurezza di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitorare le soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): risposte alle domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.
