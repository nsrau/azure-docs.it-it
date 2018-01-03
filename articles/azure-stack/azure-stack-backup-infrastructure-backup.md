---
title: Backup e ripristino dei dati per lo Stack di Azure con il servizio di Backup infrastruttura | Documenti Microsoft
description: "È possibile eseguire il backup e ripristinare i dati di configurazione e del servizio tramite il servizio di Backup di infrastruttura."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9B51A3FB-EEFC-4CD8-84A8-38C52CFAD2E4
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 84ce0d72ff826ecb3f5deff165db00a1e50ae89d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Backup e ripristino dei dati per lo Stack di Azure con il servizio di Backup di infrastruttura

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile eseguire il backup e ripristinare i dati di configurazione e del servizio tramite il servizio di Backup di infrastruttura. Ogni installazione di Azure Stack contiene un'istanza del servizio. Per ripristinare l'identità, sicurezza e i dati di gestione risorse di Azure, è possibile utilizzare i backup creati dal servizio per la ridistribuzione di Stack di Cloud di Azure.

Quando si è pronti per inserire il cloud nell'ambiente di produzione, è possibile abilitare il backup. Non consentire il backup se si prevede di eseguire i test e convalida per un lungo periodo di tempo.

Prima di abilitare il servizio di backup, assicurarsi di avere [requisiti sul posto](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Il servizio di Backup di infrastruttura non include applicazioni e dati utente. Vedere gli articoli seguenti per ulteriori informazioni sul backup e ripristino [servizi App](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), e [MySQL](https://aka.ms/azure-stack-mysql) provider di risorse e dati utente associati...

## <a name="the-infrastructure-backup-service"></a>Il servizio di Backup di infrastruttura

I servizi contiene le funzionalità seguenti.

| Funzionalità                                            | DESCRIZIONE                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Servizi di infrastruttura di backup                     | Coordinare un sottoinsieme di servizi di infrastruttura nello Stack di Azure backup. Se si verifica un'emergenza, è possano ripristinare i dati come parte di ridistribuzione. |
| La compressione e crittografia dei dati di Backup esportati | Dati di backup vengono compressi e crittografati dal sistema prima che viene esportato nel percorso di archiviazione esterno fornito dall'amministratore.                |
| Monitoraggio del processo di backup                              | Sistema di notifica quando hanno esito negativo e correzione passaggi dei processi di backup.                                                                                                |
| Esperienza di gestione dei backup                       | RP backup supporta l'attivazione di backup.                                                                                                                         |
| Ripristino di cloud                                     | Se si verifica una perdita di dati, è possono utilizzare i backup per ripristinare le informazioni di base dello Stack di Azure come parte della distribuzione.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Verificare i requisiti per il servizio di Backup di infrastruttura

- **Percorso di archiviazione**  
  È necessario una condivisione file accessibile dallo Stack di Azure che può contenere i sette backup. Ogni backup è di circa 10 GB. La condivisione deve essere in grado di archiviare 140 GB di backup. Per ulteriori informazioni sulla selezione di un percorso di archiviazione per il servizio di Backup di Azure Stack infrastruttura, vedere [requisiti del Controller di Backup](azure-stack-backup-reference.md#backup-controller-requirements).
- **Credenziali**  
  È necessario un account utente di dominio e le credenziali, ad esempio, è possibile utilizzare le credenziali di amministratore dello Stack di Azure.
- **Chiave di crittografia**  
  I file di backup vengono crittografati tramite la chiave. Assicurarsi di archiviare la chiave in un luogo sicuro. Dopo aver impostato questa chiave per la prima volta o ruotare la chiave in futuro, è possibile visualizzare la chiave da questa interfaccia. Per ulteriori istruzioni generare una chiave precondivisa, seguire gli script in [abilitare il Backup per lo Stack di Azure con PowerShell](http://azure-stack-backup-enable-backup-powershell.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [abilitare il Backup per lo Stack di Azure dal portale di amministrazione](azure-stack-backup-enable-backup-console.md).
- Informazioni su come [abilitare il Backup per lo Stack di Azure con PowerShell](azure-stack-backup-enable-backup-powershell.md).
- Informazioni su come [backup Azure Stack](azure-stack-backup-back-up-azure-stack.md )
- Informazioni su come [recupero dalla perdita di dati](azure-stack-backup-recover-data.md)
