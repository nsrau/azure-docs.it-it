---
title: Gestire Azure Key Vault tramite Automazione di Azure - Azure Key Vault | Microsoft Docs
description: Informazioni su come è possibile usare il servizio Automazione di Azure per gestire l'insieme di credenziali chiave di Azure.
services: Key-Vault, automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: magoedte
ms.openlocfilehash: ace32968808dfa919e6ca5d5777818d2672249fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306071"
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Gestione dell'insieme di credenziali chiave usando Automazione di Azure

Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'utilizzo per semplificare la gestione delle chiavi e dei segreti nell'insieme di credenziali chiave di Azure.

## <a name="what-is-azure-automation"></a>Informazioni su Automazione di Azure

[Automazione di Azure](../automation/automation-intro.md) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi e la configurazione preferita per lo stato. Usando Automazione di Azure, è possibile automatizzare attività manuali, ripetute, a esecuzione prolungata e soggette a errori per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione dell'organizzazione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio consente di ridurre i costi operativi e di liberare risorse dello staff IT e DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>In che modo è possibile gestire l'insieme di credenziali chiave di Azure con Automazione di Azure?

L'insieme di credenziali delle chiavi può essere gestito in Automazione di Azure usando i [cmdlet per l'insieme di credenziali delle chiavi di AzureRM](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) e i [cmdlet per l'insieme di credenziali delle chiavi di Azure classico](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Il modulo Azure per la gestione dell'insieme di credenziali delle chiavi classico è disponibile automaticamente in Automazione di Azure ed è possibile importare il [modulo per l'insieme di credenziali delle chiavi di AzureRM](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) in Automazione di Azure, in modo da poter eseguire molte delle attività di gestione dell'insieme di credenziali delle chiavi all'interno del servizio. Per altre informazioni su come importare un modulo in automazione di Azure, vedere [gestire i moduli in automazione di Azure](../automation/shared-resources/modules.md) è anche possibile abbinare tali cmdlet in automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in Servizi di Azure e sistemi di terze parti 3rd.

Con i cmdlet dell'insieme di credenziali delle chiavi di Azure è possibile eseguire, ad esempio, queste attività: 

* Creare e configurare un insieme di credenziali delle chiavi
* Creare o importare una chiave
* Creare o aggiornare un segreto
* Aggiornare gli attributi di una chiave
* Ottenere una chiave o un segreto
* Eliminare una chiave o un segreto

Di seguito sono riportati alcuni esempi di uso di PowerShell per gestire l'insieme di credenziali delle chiavi:  

* [Insieme di credenziali delle chiavi di Azure - Procedura dettagliata](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Installazione e configurazione di un insieme di credenziali delle chiavi di Azure](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e del modo in cui può essere usato per gestire l'insieme di credenziali chiave di Azure, usare i collegamenti seguenti per altre informazioni su Automazione di Azure.

* Vedere l' [esercitazione introduttiva](../automation/automation-first-runbook-graphical.md)di Automazione di Azure.
* Vedere gli [script di PowerShell dell'insieme di credenziali chiave di Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

