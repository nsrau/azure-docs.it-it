---
title: Configurare le impostazioni del proxy del servizio Mobility per il ripristino di emergenza da Azure ad Azure | Microsoft Docs
description: Fornisce informazioni dettagliate su come configurare il servizio Mobility quando i clienti usano un proxy nel proprio ambiente di origine.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503127"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Configurare le impostazioni del proxy del servizio Mobility per il ripristino di emergenza da Azure ad Azure

Questo articolo fornisce informazioni aggiuntive sulla personalizzazione delle configurazioni di rete nella macchina virtuale (VM) di Azure di destinazione quando si esegue la replica e il ripristino di VM di Azure da un'area a un'altra, usando [Azure Site Recovery](site-recovery-overview.md).

Lo scopo di questo documento è fornire i passaggi per configurare le impostazioni proxy per Azure Site Recovery servizio Mobility nello scenario di ripristino di emergenza da Azure ad Azure. 

I proxy sono gateway di rete che consentono o impediscono le connessioni di rete agli endpoint. Un proxy è in genere un computer all'esterno del computer client che tenta di accedere agli endpoint di rete. Un elenco di bypass consente al client di stabilire connessioni direttamente agli endpoint senza passare attraverso il proxy. È possibile impostare facoltativamente un nome utente e una password per un proxy da parte degli amministratori di rete, in modo che solo i client autenticati possano usare il proxy. 

## <a name="before-you-start"></a>Prima di iniziare

Informazioni sul ripristino di emergenza fornito da Site Recovery per [questo scenario](azure-to-azure-architecture.md).
Informazioni sulle [linee guida di rete](azure-to-azure-about-networking.md) per la replica e il ripristino di macchine virtuali di Azure da un'area a un'altra, usando [Azure Site Recovery](site-recovery-overview.md).
Verificare che il proxy sia configurato in modo appropriato in base alle esigenze dell'organizzazione.

## <a name="configure-the-mobility-service"></a>Configurare il servizio Mobility

Il servizio Mobility supporta solo proxy non autenticati. Sono disponibili due modi per immettere i dettagli del proxy per la comunicazione con Site Recovery endpoint. 

### <a name="method-1-auto-detection"></a>Metodo 1: rilevamento automatico

Il servizio Mobility rileva automaticamente le impostazioni del proxy dalle impostazioni di ambiente o dalle impostazioni di IE (solo Windows) durante l'abilitazione della replica. 

- Sistema operativo Windows: durante l'abilitazione della replica, il servizio Mobility rileva le impostazioni del proxy configurate in Internet Explorer per l'utente del sistema locale. Per configurare il proxy per l'account di sistema locale, un amministratore può utilizzare PsExec per avviare un prompt dei comandi e quindi Internet Explorer. 
- Sistema operativo Windows: le impostazioni proxy sono configurate come variabili di ambiente http_proxy e no_proxy. 
- Sistema operativo Linux: le impostazioni proxy sono configurate in/etc/profile o/etc/environment come variabili di ambiente http_proxy, no_proxy. 
- Le impostazioni proxy rilevate automaticamente vengono salvate nel file di configurazione del proxy del servizio Mobility ProxyInfo. conf 
- Percorso predefinito di ProxyInfo. conf 
    - Windows: C:\ProgramData\Microsoft Azure site Recovery\Config\ProxyInfo.conf 
    - Linux:/usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Metodo 2: specificare le impostazioni personalizzate del proxy di applicazione

In questo caso, il cliente fornisce impostazioni personalizzate del proxy dell'applicazione nel file di configurazione del servizio Mobility ProxyInfo. conf. Questo metodo consente ai clienti di fornire il proxy solo per il servizio Mobility o un proxy diverso per Azure Site Recovery servizio Mobility rispetto a un proxy (o nessun proxy) per le altre applicazioni nel computer.

## <a name="proxy-template"></a>Modello proxy
ProxyInfo. conf contiene il modello seguente [proxy] Address =http://1.2.3.4 Port = 5678 bypass = hypervrecoverymanager. WindowsAzure. com, login. microsoftonline. com, BLOB. Core. Windows. NET. L'oggetto bypass non supporta i caratteri jolly, ad esempio ' *. windows.net ', ma la concessione di windows.net è abbastanza efficace per il bypass. 

## <a name="next-steps"></a>Passaggi successivi:
- Leggere le [linee guida sulla rete](site-recovery-azure-to-azure-networking-guidance.md) per la replica di macchine virtuali di Azure.
- Distribuire il ripristino di emergenza [replicando le macchine virtuali di Azure](site-recovery-azure-to-azure.md).