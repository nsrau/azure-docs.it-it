---
title: Backup di Azure - Backup di macchine virtuali IaaS di Azure con dischi crittografati | Microsoft Docs
description: Informazioni su come Backup di Azure gestisce i dati crittografati mediante BitLocker o dmcrypt durante il backup delle macchine virtuali IaaS. Questo articolo consente di prepararsi alle differenze di backup e di ripristino quando si gestiscono dischi crittografati.
services: backup
documentationcenter: ''
author: pallavijoshi
manager: vijayts
editor: ''

ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/16/2016
ms.author: markgal; jimpark; trinadhk

---
# Gestione di dischi crittografati durante il backup delle macchine Virtuali
Per le aziende interessate a crittografare i dati delle macchine virtuali in Azure, la soluzione da usare è [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md) o Bitlocker nei computer Windows e dmcrypt nei computer Linux.

> [!NOTE]
> Backup di Azure supporta il backup e ripristino di macchine virtuali crittografate con Crittografia dischi di Azure. <br>
> 
> 1. Questa funzionalità è supportata tramite PowerShell se la macchina virtuale viene crittografata con BEK e KEK. <br>
> 2. Le operazioni di backup e ripristino non sono supportate se la macchina virtuale viene crittografata solo con BEK. <br> Vedere la [documentazione di PowerShell](backup-azure-vms-automation.md) per Backup di Azure per eseguire il backup e il ripristino di macchine virtuali crittografate con ADE.
> 
> 

Questo articolo riguarda le macchine virtuali di Azure crittografate con CloudLink.

## Funzionamento del backup
La soluzione complessiva è costituita da due livelli - il livello della macchina virtuale e il livello dell’Archiviazione.

1. Il livello di macchina virtuale gestisce i dati tali come vengono visualizzati dal sistema operativo guest e le applicazioni in esecuzione nella macchina virtuale. È inoltre il livello che esegue il software di crittografia (Bitlocker o dmcrypt), crittografando in modo trasparente i dati sui volumi prima di scriverli nei dischi.
2. Il livello dell’Archiviazione riguarda i BLOB delle pagine e i dischi collegati alla macchina virtuale. Non si dispone di informazioni sui dati da scrivere sul disco, e se essi sono stati crittografati o meno. Si tratta del livello in cui opera la funzionalità del backup delle macchine virtuali.

![Come coesistono il backup della macchina virtuale di Azure backup e la crittografia Bitlocker](./media/backup-azure-vms-encryption/how-it-works.png)

L’intera crittografia dei dati avviene in modo trasparente e senza problemi nel livello della macchina virtuale. In questo modo i dati scritti nei BLOB delle pagine collegate alla macchina virtuale sono i dati crittografati. Quando [il Backup di Azure prende uno snapshot dei dati di dischi e dei trasferimenti della macchina virtuale](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines), esso copia i dati crittografati presenti nei BLOB della pagina.

## Componenti della soluzione
Esistono molte parti di questa soluzione che devono essere configurate e gestite in modo corretto per un buon funzionamento:

| Funzione | Software utilizzato | Note aggiuntive |
| --- | --- | --- |
| Crittografia |BitLocker o dmcrypt |Poiché la crittografia avviene in un *diverso* livello quando è messa a confronto con il Backup di Azure, non importa quale software di crittografia si utilizza. Ciò premesso, questa esperienza è stata convalidata solo con CloudLink usando Bitlocker e dmcrypt.<br><br> Per crittografare i dati, è necessaria una chiave. La chiave deve anche essere mantenuta al sicuro per garantire l'accesso autorizzato ai dati. |
| Gestione della chiave |CloudLink SecureVM |La chiave è fondamentale per la crittografia o la decrittografia dei dati. È impossibile recuperare i dati senza la chiave corretta. Questo diventa *estremamente* importante con:<br><li>Attivazioni della chiave<li>Conservazione a lungo termine<br><br>Ad esempio, è possibile che la chiave usata per effettuare il backup dei dati 7 anni non sia la stessa chiave usata oggi. Se non si è in possesso della chiave usata 7 anni fa, non sarà possibile usare i dati ripristinati da quel momento. |
| Backup dei dati |Backup di Azure |Usare Backup di Azure per eseguire il backup di macchine virtuali IaaS di Azure mediante il [Portale di gestione di Azure](http://manage.windowsazure.com) o PowerShell |
| Ripristino dei dati |Backup di Azure |Usare Backup di Azure per ripristinare i dischi o un'intera macchina virtuale da un punto di ripristino. I dati non vengono decrittografati dal Backup di Azure come parte dell'operazione di ripristino. |
| Decrittografia |BitLocker o dmcrypt |Per leggere i dati da un disco dati ripristinato o da una macchina virtuale ripristinata, il software ha bisogno della chiave dal software di gestione delle chiavi. È impossibile decrittografare i dati senza la chiave corretta. |

> [!IMPORTANT]
> Gestione delle chiavi - compreso il rollover della chiave - non è una parte del Backup di Azure. Questo aspetto deve essere gestito in modo indipendente, ma è molto importante per l’operazione complessiva di backup/ripristino.
> 
> 

### Scenari Supportati
| &nbsp; | Insieme di credenziali per il backup | Insieme di credenziali dei servizi di ripristino |
|:--- |:--- |:--- |
| Macchine virtuali IaaS V1 di Azure |Sì |No |
| Macchine virtuali IaaS V2 di Azure |N/D |No |

## CloudLink SecureVM
[CloudLink SecureVM](http://www.cloudlinktech.com/choose-your-cloud/microsoft-azure/) è una soluzione di crittografia della macchina virtuale che può essere usata per proteggere i dati della macchina virtuale IaaS di Azure. CloudLink SecureVM è supportato per usare Backup di Azure.

### Informazioni di supporto
* CloudLink SecureVM versione 4.0 (build 21536.121 o versione successiva)
* Azure PowerShell 0.9.8 o versione successiva.

### Gestione della chiave
Quando è necessario effettuare il rollover o modificare le chiavi per le macchine virtuali che dispongono di backup esistenti, è necessario assicurarsi che siano disponibili le chiavi usate al momento del backup. Un modo consigliato consiste nell'eseguire un backup del keystore o l'intero sistema SecureVM.

### Documentazione e Risorse
* [Guida alla distribuzione - PDF](http://www.cloudlinktech.com/Azure/CL_SecureVM_4_0_DG_EMC_Azure_R2.pdf)
* [Distribuzione e uso di SecureVM - video](https://www.youtube.com/watch?v=8AIRe92UDNg)

<!---HONumber=AcomDC_0817_2016-->