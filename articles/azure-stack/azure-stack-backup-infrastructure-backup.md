---
title: Backup e ripristino dei dati per Azure Stack con il servizio Backup di infrastruttura | Microsoft Docs
description: È possibile eseguire il backup e ripristinare i dati di servizio usando il servizio Backup di infrastruttura e configurazione.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 005a9e3903ffa16882f8d07529bf5c4924837db5
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790849"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Backup e ripristino dei dati per Azure Stack con il servizio Backup di infrastruttura

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile eseguire il backup e ripristinare i dati di servizio usando il servizio Backup di infrastruttura e configurazione. Ogni installazione di Azure Stack contiene un'istanza del servizio. È possibile usare i backup creati dal servizio per la ridistribuzione del Cloud Azure Stack per il ripristino di identità, sicurezza e i dati di Azure Resource Manager.

È possibile abilitare il backup quando si è pronti a utilizzare il cloud nell'ambiente di produzione. Non abilitare il backup se si prevede di eseguire il test e convalida per un lungo periodo di tempo.

Prima di abilitare il servizio backup, assicurarsi di avere [requisiti posto](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Il servizio di infrastruttura di Backup non include applicazioni e dati utente. <!-- See the following articles for instructions on backing up and restore [App Services](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), and [MySQL](https://aka.ms/azure-stack-mysql) resource providers and associated user data. -->

## <a name="the-infrastructure-backup-service"></a>Il servizio Backup di infrastruttura

I servizi contengono le funzionalità seguenti.

| Funzionalità                                            | DESCRIZIONE                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Servizi di infrastruttura di backup                     | Backup coordinato attraverso un subset dei servizi di infrastruttura in Azure Stack. Se si verifica una situazione di emergenza, i dati possono essere ripristinati come parte di ridistribuzione. |
| La compressione e la crittografia dei dati di Backup esportati | I dati di backup vengono compressi e crittografati dal sistema prima che venga esportato nel percorso di archiviazione esterno fornito dall'amministratore.                |
| Monitoraggio dei processi di backup                              | Sistema di notifica quando hanno esito negativo e la correzione passaggi dei processi di backup.                                                                                                |
| Esperienza di gestione di backup                       | Applicazione relying Party backup supporta l'abilitazione del backup.                                                                                                                         |
| Ripristino di cloud                                     | Se si verifica una grave perdita dei dati, backup possono essere usati per ripristinare le informazioni di Azure Stack core come parte della distribuzione.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Verificare i requisiti per il servizio Backup di infrastruttura

- **Posizione di archiviazione**  
  È necessaria una condivisione di file accessibili da Azure Stack che può contenere i backup di sette. Ogni backup è di circa 10 GB. La condivisione deve essere in grado di archiviare 140 GB di backup. Per altre informazioni sulla selezione di un percorso di archiviazione per il servizio Backup di Azure Stack dell'infrastruttura, vedere [requisiti del Controller Backup](azure-stack-backup-reference.md#backup-controller-requirements).
- **Credenziali**  
  È necessario un account utente di dominio e le credenziali, ad esempio, è possibile usare le credenziali di amministratore di Azure Stack.
- **Certificato di crittografia**  
  I file di backup vengono crittografati usando la chiave pubblica nel certificato. Assicurarsi di archiviare il certificato in un luogo sicuro. 


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [abilitare il Backup per Azure Stack dal portale di amministrazione](azure-stack-backup-enable-backup-console.md).

Informazioni su come [abilitare il Backup per Azure Stack con PowerShell](azure-stack-backup-enable-backup-powershell.md).

Informazioni su come [eseguire il backup di Azure Stack](azure-stack-backup-back-up-azure-stack.md )

Informazioni su come [risarcimento grave perdita dei dati](azure-stack-backup-recover-data.md)
