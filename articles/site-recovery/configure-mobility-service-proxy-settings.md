---
title: Configurare le impostazioni del proxy del servizio mobility per Azure al ripristino di emergenza di Azure Documenti Microsoft
description: Vengono fornite informazioni dettagliate su come configurare il servizio per dispositivi mobili quando i clienti utilizzano un proxy nell'ambiente di origine.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503127"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Configurare le impostazioni del proxy del servizio mobility per Azure al ripristino di emergenza di AzureConfigure Mobility Service Proxy Settings for Azure to Azure Disaster Recovery

Questo articolo fornisce indicazioni sulla personalizzazione delle configurazioni di rete nella macchina virtuale (VM) di Azure di destinazione durante la replica e il ripristino di macchine virtuali di Azure da un'area a un'altra, usando [Azure Site Recovery.This](site-recovery-overview.md)article provides guidance on customizing networking configurations on the target Azure virtual machine (VM) when you're replicating and recovering Azure VMs from one region to another, using Azure Site Recovery .

Lo scopo di questo documento è fornire i passaggi per configurare le impostazioni proxy per il servizio per dispositivi mobili di Azure Site Recovery nello scenario Azure-Ripristino di emergenza. 

I proxy sono gateway di rete che consentono/non consentono connessioni di rete agli endpoint. In genere un proxy è un computer esterno al computer client che tenta di accedere agli endpoint di rete. Un elenco di esclusione consente al client di stabilire connessioni direttamente agli endpoint senza passare attraverso il proxy. Un nome utente e una password possono essere impostati facoltativamente per un proxy dagli amministratori di rete in modo che solo i client autenticati possano utilizzare proxy. 

## <a name="before-you-start"></a>Prima di iniziare

Informazioni sul ripristino di emergenza fornito da Site Recovery per [questo scenario](azure-to-azure-architecture.md).
Comprendere le [linee guida](azure-to-azure-about-networking.md) per la rete durante la replica e il ripristino di macchine virtuali di Azure da un'area a un'altra, usando Azure Site Recovery.Understand the networking guidance when you're replicating and recovering Azure VMs from one region to another, using [Azure Site Recovery](site-recovery-overview.md).
Assicurarsi che il proxy sia configurato in modo appropriato in base alle esigenze dell'organizzazione.

## <a name="configure-the-mobility-service"></a>Configurare il servizio Mobility

Il servizio Mobility supporta solo proxy non autenticati. Fornisce due modi per immettere i dettagli del proxy per la comunicazione con gli endpoint di Site Recovery. 

### <a name="method-1-auto-detection"></a>Metodo 1: Rilevamento automatico

Servizio Mobility rileva automaticamente le impostazioni proxy dalle impostazioni di ambiente o dalle impostazioni di Internet Explorer (solo Windows) durante l'abilitazione della replica. 

- Sistema operativo Windows: durante l'abilitazione della replica, il servizio Mobility rileva le impostazioni proxy come configurate in Internet Explorer per l'utente del sistema locale. Per impostare il proxy per l'account di sistema locale, un amministratore può utilizzare psexec per avviare un prompt dei comandi e quindi Internet Explorer. 
- Sistema operativo Windows: le impostazioni proxy sono configurate come variabili di ambiente http_proxy e no_proxy. 
- Linux OS: le impostazioni proxy sono configurate in /etc/profile o /etc/environment come variabili di ambiente http_proxy, no_proxy. 
- Le impostazioni proxy rilevate automaticamente vengono salvate nel file di configurazione proxy del servizio per dispositivi mobili ProxyInfo.conf 
- Percorso predefinito di ProxyInfo.conf 
    - Windows: C: . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 
    - Linux: /usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Metodo 2: Fornire le impostazioni del proxy di applicazione personalizzato

In questo caso, il cliente fornisce impostazioni proxy di applicazione personalizzate nel file di configurazione del servizio Mobility ProxyInfo.conf. Questo metodo consente ai clienti di fornire proxy solo per il servizio per dispositivi mobili o un proxy diverso per il servizio per dispositivi mobili di Azure Site Recovery rispetto a un proxy (o nessun proxy) per il resto delle applicazioni nel computer.

## <a name="proxy-template"></a>Modello proxy
ProxyInfo.conf contiene il modello seguentehttp://1.2.3.4 [proxy] Address , Port , 5678 BypassList , hypervrecoverymanager.windowsazure.com, login.microsoftonline.com,blob.core.windows.net. Il BypassList non supporta i caratteri jolly come '.windows.net', ma dando windows.net è abbastanza buono per bypassare. 

## <a name="next-steps"></a>Passaggi successivi:
- Leggere [le indicazioni sulla rete](site-recovery-azure-to-azure-networking-guidance.md) per la replica delle macchine virtuali di Azure.Read networking guidance for replicating Azure VMs.
- Distribuire il ripristino di emergenza [replicando le macchine virtuali di Azure](site-recovery-azure-to-azure.md).