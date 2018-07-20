---
title: Integrità dell'infrastruttura di Azure
description: Questo articolo descrive l'integrità dell'infrastruttura di Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 867bc66a68bec662153d8336e649cf46df02f101
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901317"
---
# <a name="azure-infrastructure-integrity"></a>Integrità dell'infrastruttura di Azure

## <a name="software-installation"></a>Installazione del software
Tutti i componenti nello stack del software installati nell'ambiente di Azure sono compilati in modo personalizzato in base al processo di Microsoft Security Development Lifecycle (SDL). Tutti i componenti software, inclusi immagini del sistema operativo e il database SQL, vengono distribuiti come parte del processo di modifica e di gestione del rilascio. Il sistema operativo eseguito in tutti i nodi è una versione personalizzata di Windows Server 2008 o Windows Server 2012. La versione corretta è selezionata per il controller di infrastruttura in base al ruolo previsto per il sistema operativo. Il sistema operativo host non consente l'installazione di tutti i componenti software non autorizzati.

Alcuni componenti di Microsoft Azure (ad esempio RDFE, portale per sviluppatori e così via) vengono distribuiti come clienti di Azure nella macchina virtuale guest in esecuzione nel sistema operativo guest.

## <a name="virus-scans-on-builds"></a>Ricerca di virus nelle compilazioni
Le compilazioni dei componenti software di Azure (inclusi i sistemi operativi) devono essere sottoposti a una ricerca di virus con lo strumento antivirus Microsoft Endpoint Protection (MEP). Ogni ricerca di virus crea un log nella directory di compilazione associata, che indica in dettaglio gli elementi analizzati e i risultati della ricerca. La ricerca di virus fa parte del codice sorgente della compilazione per ogni componente in Azure. Il codice non viene spostato nella produzione se prima non è stato sottoposto a una ricerca di virus con esito positivo. Se sono presenti eventuali problemi segnalati, la compilazione viene bloccata e quindi passa ai team di sicurezza di Microsoft per identificare il punto della compilazione in cui è presente il codice inaffidabile.

## <a name="closedlocked-environment"></a>Ambiente chiuso/bloccato
Per impostazione predefinita, per i nodi dell'infrastruttura di Azure e per le macchine virtuali guest non sono creati account. Sono disabilitati anche gli account amministratore di Windows predefiniti. Con le autorizzazioni di autenticazione appropriate, gli amministratori di Microsoft Azure Live Support (WALS) possono accedere a tali macchine e amministrare la rete di produzione di Azure per ripristini di emergenza.

## <a name="microsoft-azure-sql-database-authentication"></a>Autenticazione del database SQL di Microsoft Azure
In modo analogo a qualsiasi implementazione di SQL Server, la gestione degli account utente deve essere controllata attentamente. Il database SQL di Microsoft Azure supporta solo l'autenticazione di SQL Server. Gli account utente con password complesse e configurati con specifici diritti devono essere usati anche come complemento del modello di sicurezza dei dati del cliente.

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>Firewall ed elenchi di controllo di accesso tra il cluster della rete aziendale MSFT e di Microsoft Azure
Il firewall e gli elenchi di controllo di accesso tra la piattaforma del servizio e la rete aziendale Microsoft proteggono il database SQL di Microsoft Azure da accessi non autorizzati. Solo gli utenti appartenenti a intervalli di indirizzi IP della rete aziendale Microsoft possono accedere all'endpoint di gestione della piattaforma WinFabric.

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Firewall ed elenchi di controllo di accesso tra nodi in un cluster di database SQL di Azure
Come protezione aggiuntiva, nell'ambito di una strategia di difesa più avanzata, il firewall e gli elenchi di controllo di accesso sono stati implementati tra nodi in un cluster di database SQL di Microsoft Azure. Tutte le comunicazioni all'interno del cluster della piattaforma WinFabric e tutto il codice in esecuzione sono attendibili.

## <a name="custom-mas-watchdogs"></a>Agenti di gestione personalizzati (watchdog)
Nel database SQL di Microsoft Azure vengono usati agenti di gestione personalizzati denominati watchdog per monitorare l'integrità del cluster di database SQL di Microsoft Azure.

## <a name="web-protocols"></a>Protocolli Web

### <a name="role-instance-monitoring-and-restart"></a>Monitoraggio e riavvio delle istanze del ruolo
Azure garantisce che tutti i ruoli in esecuzione (ruoli Web con connessione Internet o ruoli di lavoro di elaborazione back-end) distribuiti siano soggetti al monitoraggio per assicurarne un'integrità continua e per verificare che offrano in modo efficiente i servizi in cui ne è stato eseguito il provisioning. Nel caso in cui un ruolo perda l'integrità, ad esempio a causa di un errore critico dell'applicazione ospitata o di un problema di configurazione sottostante nell'istanza del ruolo stesso, il controller di infrastruttura di Microsoft Azure rileva il problema nell'istanza del ruolo e avvia uno stato di correzione.

### <a name="compute-connectivity"></a>Connettività di calcolo
Azure garantisce che l'applicazione o il servizio distribuito sia raggiungibile tramite protocolli standard basati sul Web. Le istanze virtuali del ruolo Web con connessione Internet dispongono di connettività Internet esterna e sono raggiungibili direttamente dagli utenti Web. Le istanze virtuali del ruolo di lavoro di elaborazione back-end dispongono di connettività Internet esterna, ma non sono accessibili direttamente da un utente Web esterno al fine di proteggere la riservatezza e l'integrità delle operazioni che i ruoli di lavoro eseguono per conto delle istanze virtuali dei ruoli Web accessibili pubblicamente.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Disponibilità dell'infrastruttura di Azure](azure-infrastructure-availability.md)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Microsoft Azure](azure-infrastructure-sql.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoring of Azure infrastructure](azure-infrastructure-monitoring.md) (Monitoraggio dell'infrastruttura di Azure)
- [Protezione dei dati dei clienti in Azure](azure-protection-of-customer-data.md)
