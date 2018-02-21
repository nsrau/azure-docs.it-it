---
title: Abilitare i gruppi di sicurezza di rete nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come implementare la raccomandazione **Abilita i gruppi di sicurezza di rete** del Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 1e034d59d8847f237fa0d4c772344d45cd618576
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Abilitare i gruppi di sicurezza di rete nel Centro sicurezza di Azure
Se non è già disponibile, il Centro sicurezza di Azure consiglia l'abilitazione di un gruppo di sicurezza di rete. I gruppi di sicurezza di rete contengono un elenco di regole dell'elenco di controllo di accesso (ACL) che consentono o rifiutano il traffico di rete alle istanze VM in una rete virtuale. I gruppi di sicurezza di rete possono essere associati a subnet o singole istanze VM in una subnet. Quando un gruppo di sicurezza di rete viene associato a una subnet, le regole ACL si applicano a tutte le istanze di VM in tale subnet. Il traffico verso una singola VM può essere inoltre ulteriormente limitato associando un gruppo di sicurezza di rete direttamente a tale VM. Per altre informazioni, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)

Se i gruppi di sicurezza di rete non sono stati abilitati, il Centro sicurezza presenta all'utente due indicazioni, ovvero Abilita i gruppi di sicurezza di rete nelle subnet e Abilita i gruppi di sicurezza di rete sulle macchine virtuali. È possibile scegliere il livello a cui applicare i gruppi di sicurezza di rete, ovvero subnet o VM.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questa non è una guida dettagliata.
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nel pannello **Raccomandazioni** selezionare **Abilita i gruppi di sicurezza di rete** nelle subnet o sulle macchine virtuali.
   ![Abilitare i gruppi di sicurezza di rete][1]
2. Verrà visualizzato il pannello **Configura i gruppi di sicurezza di rete mancanti** per le subnet o le macchine virtuali, in base alla raccomandazione selezionata. Selezionare una subnet o una macchina virtuale su cui configurare un gruppo di sicurezza di rete.

   ![Configurare i gruppi di sicurezza di rete per la subnet][2]

   ![Configurare i gruppi di sicurezza di rete per le macchine virtuali][3]
3. Nel pannello **Scegli un gruppo di sicurezza di rete** selezionare un gruppo di sicurezza di rete esistente o selezionare **Crea nuovo** per crearne uno nuovo.

   ![Scegli un gruppo di sicurezza di rete][4]

Se si crea un gruppo di sicurezza di rete, seguire la procedura illustrata in [Come gestire gruppi di sicurezza di rete tramite il portale di Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) per creare un gruppo di sicurezza di rete e configurare le regole di sicurezza.

## <a name="see-also"></a>Vedere anche 
Questo articolo ha illustrato come implementare la raccomandazione "Abilita i gruppi di sicurezza di rete" per subnet o macchine virtuali nel Centro sicurezza. Per altre informazioni sull'abilitazione dei gruppi di sicurezza di rete, vedere gli articoli seguenti:

* [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)
* [Come gestire gruppi di sicurezza di rete tramite il portale di Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
