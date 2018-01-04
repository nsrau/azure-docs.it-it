---
title: Aggiornare un insieme di credenziali di backup a un insieme di credenziali di Servizi di ripristino di Backup di Azure | Microsoft Docs
description: "Aggiornare un insieme di credenziali di backup a un insieme di credenziali di Servizi di ripristino per disporre di nuove funzionalità quali, ad esempio, il backup di macchine virtuali di Resource Manager, la protezione avanzata, il backup di macchine virtuali VMware e il backup dello stato del sistema per server Windows"
services: backup
documentationcenter: 
author: trinadhk
manager: vijayts
editor: 
keyword: backup vault; upgrade vault; recovery services vault
ms.assetid: d037a8bf-49f2-4578-974a-3471d87ca278
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: trinadhk, sogup
ms.openlocfilehash: 708983fc2c5264d1213bdb32b665dcccc5ca9df9
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Insieme di credenziali di backup aggiornato a un insieme di credenziali di Servizi di ripristino
In questo articolo viene fornita una panoramica delle caratteristiche dell'insieme di credenziali di Servizi di ripristino, le domande frequenti relative all'aggiornamento dell'insieme di credenziali di backup esistente all'insieme di credenziali di Servizi di ripristino e i passaggi successivi all'aggiornamento. Un insieme di credenziali di Servizi di ripristino è l'equivalente di Azure Resource Manager di un insieme di credenziali di backup che contiene i dati di backup. I dati sono in genere copie di dati o informazioni di configurazione per macchine virtuali, carichi di lavoro, server o workstation, sia locali che in Azure.

## <a name="what-is-a-recovery-services-vault"></a>Informazioni sull'insieme di credenziali di Servizi di ripristino
Un insieme di credenziali di Servizi di ripristino è un'entità di archiviazione online in Azure usata per contenere dati, ad esempio copie di backup, punti di ripristino e criteri di backup. È possibile usare gli insiemi di credenziali di Servizi di ripristino per contenere dati di backup per vari servizi di Azure, ad esempio database SQL Azure e macchine virtuali IaaS, Linux o Windows. Gli insiemi di credenziali di Servizi di ripristino supportano System Center DPM, Windows Server, server di Backup di Azure e altro ancora. Gli insiemi di credenziali di Servizi di ripristino semplificano l'organizzazione dei dati di backup, riducendo al minimo l'overhead di gestione.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Confronto tra gli insiemi di credenziali di Servizi di ripristino e gli insiemi di credenziali di Backup
Gli insiemi di credenziali di Servizi di ripristino si basano sul modello di Azure Resource Manager, mentre gli insiemi di credenziali di Backup si basano sul modello di Service Manager di Azure. Quando si aggiorna un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino, i dati di backup rimangono intatti durante e dopo il processo di aggiornamento. Gli insiemi di credenziali di Servizi di ripristino offrono le funzionalità non disponibili per gli insiemi di credenziali di Backup, ad esempio:

- **Funzionalità avanzate per proteggere i dati di backup**: con gli insiemi di credenziali di Servizi di ripristino, Backup di Azure offre funzionalità di sicurezza per proteggere i backup nel cloud. Queste funzionalità di sicurezza garantiscono la protezione dei backup e ripristinano i dati in modo sicuro dai backup nel cloud anche se i server di produzione e di backup vengono compromessi. [Altre informazioni](backup-azure-security-feature.md)

- **Monitoraggio centralizzato per l'ambiente IT ibrido**: con gli insiemi di credenziali di Servizi di ripristino, è possibile monitorare non solo le [macchine virtuali IaaS di Azure](backup-azure-manage-vms.md) ma anche le [risorse locali](backup-azure-manage-windows-server.md#manage-backup-items) da un portale centrale. [Altre informazioni](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Controllo degli accessi in base al ruolo o RBAC** : il controllo degli accessi in base al ruolo consente un controllo della gestione degli accessi con granularità fine in Azure. [Azure offre diversi ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md) mentre Backup di Azure dispone di tre [ruoli predefiniti per la gestione dei punti di ripristino](backup-rbac-rs-vault.md). Gli insiemi di credenziali di Servizi di ripristino sono compatibili con il controllo degli accessi in base al ruolo, che consente di limitare il backup e ripristinare l'accesso a insiemi definiti di ruoli utente. [Altre informazioni](backup-rbac-rs-vault.md)

- **Protezione di tutte le configurazioni delle macchine virtuali di Azure**: gli insiemi di credenziali di Servizi di ripristino proteggono le macchine virtuali basate su Resource Manager tra cui i dischi Premium, i dischi gestiti e le macchine virtuali crittografate. L'aggiornamento di un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino offre l'opportunità di aggiornare le macchine virtuali basate su Service Manager alle macchine virtuali basate su Resource Manager. Durante l'aggiornamento dell'insieme di credenziali, è possibile mantenere i punti di ripristino della macchina virtuale basata su Service Manager e configurare la protezione per le macchine virtuali aggiornate abilitate per Resource Manager. [Altre informazioni](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Ripristino immediato delle macchine virtuali IaaS**: con gli insiemi di credenziali di Servizi di ripristino, è possibile ripristinare file e cartelle da una macchina virtuale IaaS senza ripristinare l'intera macchina virtuale, il che consente di avere tempi di ripristino più rapidi. Il ripristino immediato per le macchine virtuali IaaS è disponibile sia per le macchine virtuali Windows che Linux. [Altre informazioni](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Se sono presenti elementi registrati in un insieme di credenziali di backup con la versione precedente a 2.0.9083.0 dell'agente MARS, [scaricare la versione più recente dell'agente MARS]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) per poter accedere a tutte le funzionalità dell'insieme di credenziali di Servizi di ripristino. 
> 

## <a name="managing-your-recovery-services-vaults"></a>Gestione degli insiemi di credenziali dei servizi di ripristino
Le schermate seguenti mostrano un nuovo insieme di credenziali di Servizi di ripristino, aggiornato dall'insieme di credenziali di Backup, nel portale di Azure. L'insieme di credenziali aggiornato sarà presente in un gruppo di risorse predefinito denominato "Default-RecoveryServices-ResourceGroup-geo". Esempio: se l'insieme di credenziali di backup si trova negli Stati Uniti occidentali, verrà inserito in un gruppo di risorse denominato campo rimarrà in RG denominato "Default-RecoveryServices-ResourceGroup-westus".
> [!NOTE]
> Per i clienti di Cloud Platform System Standard il gruppo di risorse non viene modificato dopo l'aggiornamento dell'insieme di credenziali e pertanto rimane invariato.

La prima schermata mostra il dashboard dell'insieme di credenziali che visualizza le entità principali per l'insieme di credenziali.
![esempio dell'insieme di credenziali di Servizi di ripristino aggiornato da un insieme di credenziali di backup](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

La seconda schermata mostra i collegamenti della guida disponibili che consentono di iniziare a usare l'insieme di credenziali di Servizi di ripristino.

![collegamenti alla guida del pannello Avvio rapido](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Passaggi successivi all'aggiornamento
L'insieme di credenziali di Servizi di ripristino supporta l'indicazione delle informazioni sul fuso orario nei criteri di backup. Al termine dell'aggiornamento corretto dell'insieme di credenziali, passare al menu delle impostazioni dell'insieme di credenziali dai criteri di Backup e aggiornare le informazioni sul fuso orario per ogni criterio configurato nell'insieme di credenziali. Questa schermata mostra già l'ora di pianificazione del backup specificata in base al fuso orario locale usato durante la creazione del criterio. 

## <a name="enhanced-security"></a>Sicurezza avanzata
Quando un insieme di credenziali di Backup viene aggiornato a un insieme di credenziali di Servizi di ripristino, le impostazioni di sicurezza per tale insieme di credenziali vengono attivate automaticamente. Quando le impostazioni di sicurezza sono attivate, determinate operazioni, ad esempio l'eliminazione di backup o la modifica di una passphrase, richiedono un PIN di [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md). Per altre informazioni sulla sicurezza avanzata, vedere l'articolo [Funzionalità di sicurezza per la protezione dei backup ibridi](backup-azure-security-feature.md). Quando la sicurezza avanzata è attivata, i dati vengono conservati fino a 14 giorni dopo l'eliminazione delle informazioni sul punto di recupero dall'insieme di credenziali. Ai clienti viene fatturato lo spazio di archiviazione usato per questi dati sulla sicurezza. La conservazione dei dati sulla sicurezza è applicabile ai punti di recupero acquisiti dall'agente di Backup di Azure, dal server di Backup di Azure e da System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Raccogliere i dati nell'insieme di credenziali
Dopo avere eseguito l'aggiornamento a un insieme di credenziali di Servizi di ripristino, configurare i report per Backup di Azure (per macchine virtuali IaaS e l'agente Servizi di ripristino di Microsoft Azure) e quindi usare Power BI per accedere ai report. Per altre informazioni sulla raccolta dei dati, vedere l'articolo [Configurare report di Backup di Azure](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Domande frequenti

**Il piano di aggiornamento influenza il backup in corso?**</br>
di serie I backup in corso proseguono senza interruzioni durante e dopo l'aggiornamento.

**Qual è l'impatto di questo aggiornamento per gli strumenti esistenti?**</br>
È necessario aggiornare l'automazione o gli strumenti esistenti al modello di distribuzione Resource Manager per accertarsi che continuino a funzionare anche dopo l'aggiornamento. Consultare i riferimenti di cmdlet di PowerShell per la [il modello di distribuzione di gestione risorse](backup-client-automation.md).

**È possibile eseguire il ripristino dello stato precedente dopo l'aggiornamento?**</br>
di serie Il ripristino dello stato precedente non è supportato dopo il completamento dell'aggiornamento delle risorse.

**È possibile visualizzare l'insieme di credenziali classico in seguito all'aggiornamento?**</br>
di serie Non è possibile visualizzare o gestire l'insieme di credenziali classico in seguito all'aggiornamento. L'utente potrà usare il nuovo portale di Azure solo per tutte le operazioni di gestione nell'insieme di credenziali.

**Perché non è possibile visualizzare i server protetti dall'agente MARS nell'insieme di credenziali aggiornato?**</br>
È necessario installare la versione più recente dell'agente MARS per visualizzare tutti i server protetti dall'agente stesso nell'insieme di credenziali. È possibile scaricare l'ultima versione dell'agente [qui]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**Non è possibile visualizzare i criteri di backup per i server protetti dall'agente MARS dopo l'aggiornamento**</br>
I criteri di backup dell'insieme di credenziali potrebbero non essere aggiornati e pertanto non sincronizzati con l'insieme di credenziali aggiornato. Aggiornare i criteri per continuare a visualizzare i criteri nell'insieme di credenziali aggiornato.
Per aggiornare i criteri, accedere all'agente MARS e aggiornare i criteri di backup configurati.

**Perché non è possibile aggiornare i criteri di backup dopo l'aggiornamento?**</br>
Ciò si verifica quando si usa una versione non aggiornata dell'agente di backup e si seleziona un periodo di memorizzazione minimo inferiore al valore minimo consentito. Quando un insieme di credenziali di Backup viene aggiornato a un insieme di credenziali di Servizi di ripristino, le impostazioni di sicurezza per tale insieme di credenziali vengono attivate automaticamente. Per assicurarsi che sia sempre disponibile un numero valido di punti di ripristino, è necessario configurare un periodo di memorizzazione minimo in base alla funzionalità di sicurezza in uso. Per altri dettagli, fare clic [qui](backup-azure-security-feature.md).
È anche necessario aggiornare gli agenti di Backup di Azure alla versione più recente per poter accedere alle funzionalità più aggiornate di Backup di Azure.

**Anche dopo aver eseguito l'aggiornamento dell'agente, non è possibile visualizzare gli oggetti sincronizzati nei giorni successivi all'aggiornamento**</br>
Controllare se lo stesso computer è stato registrato in più insiemi di credenziali. Controllare che si stia utilizzando lo stesso insieme di credenziali con cui l'agente MARS è stato registrato. Per sapere con quale insieme di credenziali è registrato l'agente MARS, aprire il Registro di sistema di Windows e controllare il valore della chiave ServiceResourceName in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config. In questa chiave viene visualizzato l'insieme di credenziali con cui è stato registrato l'agente MARS. Se la chiave ServiceResourceName non è visibile nel sistema, per la risoluzione del problema contattare Microsoft e comunicare il valore delle chiavi ResourceId e MachineId in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config.

**Perché non è possibile visualizzare le informazioni sui processi per le risorse in seguito all'aggiornamento?**</br>
Il monitoraggio dei backup (agente MARS e IaaS) è una nuova funzionalità che si ottiene quando si aggiorna l'insieme di credenziali di Backup all'insieme di credenziali di Servizi di ripristino. La sincronizzazione delle informazioni sul monitoraggio con il servizio richiede fino a 12 ore.

**Come si segnala un problema?**</br>
In caso di errore di una parte dell'aggiornamento dell'insieme di credenziali, annotare il valore OperationId elencato nell'errore. Il supporto tecnico Microsoft si impegnerà in modo proattivo per risolvere il problema. È possibile contattare il supporto tecnico o inviare un messaggio di posta elettronica all'indirizzo rsvaultupgrade@service.microsoft.com indicando l'ID della sottoscrizione, il nome dell'insieme di credenziali e l'ID dell'operazione. Microsoft si impegnerà per cercare di risolvere il problema il prima possibile. Non ripetere l'operazione a meno che non venga esplicitamente richiesto da Microsoft.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:</br>
[Eseguire il backup di una macchina virtuale IaaS](backup-azure-arm-vms-prepare.md)</br>
[Eseguire il backup del server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</br>
[Eseguire il backup di Windows Server](backup-configure-vault.md)
