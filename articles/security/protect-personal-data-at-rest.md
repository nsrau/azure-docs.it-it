---
title: Proteggere i dati personali inattivi in Azure con la crittografia | Microsoft Docs
description: Questo articolo fa parte di una serie che consente di usare Azure per proteggere i dati personali
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 31e41f9befd9319115e5d147b473756486100c6e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-at-rest-with-encryption"></a>Tecnologie di crittografia di Azure: proteggere i dati personali inattivi con la crittografia

Questo articolo aiuta a comprendere e usare le tecnologie di crittografia di Azure per proteggere i dati inattivi.

La crittografia dei dati inattivi è una procedura consigliata essenziale per proteggere dati sensibili o personali e per soddisfare la conformità e i requisiti di privacy dei dati.
La crittografia dei dati inattivi è progettata per impedire a un utente malintenzionato di accedere ai dati non crittografati, garantendo che i dati siano crittografati quando sono su disco.

## <a name="scenario"></a>Scenario 

Un'importante compagnia di viaggi in crociera, con sede negli Stati Uniti, sta espandendo le proprie operazioni per offrire itinerari nel Mar Mediterraneo e nel Mar Baltico, nonché nelle isole britanniche. Per supportare questo progetto, ha acquistato diverse linee minori con sede in Italia, Germania, Danimarca e Regno Unito.

La società usa Microsoft Azure per archiviare i dati aziendali nel cloud. I dati possono includere informazioni su clienti e/o dipendenti, tra cui:

- Indirizzi
- Numeri di telefono
- Codici fiscali
- Dati delle carte di credito

La società deve proteggere la privacy dei dati dei propri clienti e dipendenti rendendoli accessibili ai reparti che ne hanno bisogno, ad esempio quelli che si occupano di retribuzioni e prenotazioni.

La linea di crociere gestisce anche un database di grandi dimensioni dei membri dei programmi fedeltà e premi, che include informazioni personali per tenere traccia delle relazioni con i clienti attuali e del passato.

### <a name="problem-statement"></a>Presentazione del problema

La società deve proteggere la privacy dei dati personali di clienti e dipendenti rendendoli accessibili ai reparti che ne hanno bisogno, ad esempio quelli che si occupano di retribuzioni e prenotazioni. Questi dati personali vengono archiviati esternamente al data center controllato dall'azienda e non vengono controllati fisicamente dalla società.

### <a name="company-goal"></a>Obiettivo dell'azienda

Come parte di una strategia di sicurezza con difesa avanzata a più livelli, l'obiettivo della società è garantire che tutte le origini dati che contengono dati personali siano crittografate, incluse quelle che si trovano nello spazio di archiviazione cloud. Se utenti non autorizzati ottengono l'accesso a dati personali, questi devono essere in forma illeggibile. L'applicazione della crittografia deve essere facile, o trasparente, per utenti e amministratori.

## <a name="solutions"></a>Soluzioni

I servizi di Azure offrono più strumenti e tecnologie per semplificare la protezione dei dati personali inattivi tramite la crittografia.

### <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) offre archiviazione sicura per le chiavi usate per crittografare dati inattivi nei servizi di Azure ed è la soluzione di gestione e archiviazione delle chiavi consigliata. La gestione delle chiavi di crittografia è essenziale per proteggere i dati archiviati.

#### <a name="how-do-i-use-azure-key-vault-to-protect-keys-that-encrypt-personal-data"></a>Come si usa Azure Key Vault per proteggere le chiavi che crittografano i dati personali?

Per usare Azure Key Vault, è necessaria la sottoscrizione di un account Azure. Deve essere installato anche Azure PowerShell. I passaggi includono l'uso dei cmdlet di PowerShell per eseguire le operazioni seguenti:

1. Connettersi alle sottoscrizioni

2. Creare un insieme di credenziali delle chiavi

3. Aggiungere una chiave o un segreto all'insieme di credenziali delle chiavi

4. Registrare le applicazioni che useranno l'insieme di credenziali delle chiavi con Azure Active Directory

5. Autorizzare le applicazioni a usare la chiave o il segreto

Per creare un insieme di credenziali, usare il cmdlet di PowerShell New-AzureRmKeyVault. È necessario assegnare un nome dell'insieme di credenziali, un nome del gruppo di risorse e una posizione geografica. Il nome dell'insieme di credenziali verrà usato per la gestione delle chiavi con altri cmdlet. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

Azure Key Vault può fornire una chiave protetta tramite software oppure è possibile importare una chiave esistente in un file PFX. È anche possibile archiviare segreti (password) nell'insieme di credenziali.

È infine possibile generare una chiave nel modulo HSM locale e trasferirla nei moduli HSM nel servizio Azure Key Vault, senza che la chiave oltrepassi i confini del modulo HSM.

Per istruzioni dettagliate sull'uso di Azure Key Vault, seguire i passaggi inclusi in [Introduzione ad Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)

Per un elenco dei cmdlet di PowerShell usati con Azure Key Vault, vedere [AzureRM.KeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

### <a name="azure-disk-encryption-for-windows"></a>Crittografia dischi di Azure per Windows

[Crittografia dischi di Azure per macchine virtuali IaaS Windows e Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) protegge i dati personali inattivi nelle macchine virtuali di Azure e si integra con Azure Key Vault. Crittografia dischi di Azure usa [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) in Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) in Linux per crittografare i dischi del sistema operativo e dei dati. Crittografia dischi di Azure è supportata in Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 e nei client Windows 8 e Windows 10.

#### <a name="how-do-i-use-azure-disk-encryption-to-protect-personal-data"></a>Come si usa Crittografia dischi di Azure per proteggere i dati personali?

Per usare Crittografia dischi di Azure, è necessaria la sottoscrizione di un account Azure. Per abilitare Crittografia dischi di Azure per macchine virtuali Windows e Linux, eseguire le operazioni seguenti:

1. Usare il modello di Resource Manager per Crittografia dischi di Azure, PowerShell o l'interfaccia della riga di comando per abilitare la crittografia dei dischi e specificare la configurazione della crittografia. 

2. Concedere l'accesso alla piattaforma Azure per leggere il materiale di crittografia dall'insieme di credenziali delle chiavi.

3. Specificare l'identità di un'applicazione di Azure Active Directory (AAD) per scrivere il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi.

Azure aggiorna la configurazione della macchina virtuale e dell'insieme di credenziali delle chiavi e configura la macchina virtuale crittografata.

Quando si configura l'insieme di credenziali delle chiavi per supportare Crittografia dischi di Azure, è possibile aggiungere una chiave di crittografia della chiave per fornire ulteriore sicurezza e supportare il backup delle macchine virtuali crittografate.

![](media/protect-personal-data-at-rest/create-key.png)

Le istruzioni dettagliate per esperienze utente e scenari di distribuzione specifici sono disponibili in [Crittografia dischi di Azure per le macchine virtuali IaaS Windows e Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

### <a name="azure-storage-service-encryption"></a>Crittografia del servizio di archiviazione di Azure

[Crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/azure/storage/storage-service-encryption) consente di proteggere e salvaguardare i dati, in modo da soddisfare i criteri di sicurezza e conformità dell'organizzazione. Archiviazione di Azure crittografa automaticamente i dati usando crittografia AES a 256 bit prima della persistenza nella risorsa di archiviazione e li decrittografa prima del recupero. Questo servizio è disponibile per BLOB di Azure e File di Azure.

#### <a name="how-do-i-use-storage-service-encryption-to-protect-personal-data"></a>Come si usa Crittografia del servizio di archiviazione per proteggere i dati personali?

Per abilitare Crittografia del servizio di archiviazione, eseguire le operazioni seguenti:

1. Accedere al portale di Azure.

2. Selezionare un account di archiviazione.

3. Nella sezione Servizio BLOB in Impostazioni selezionare Crittografia.

4. Nella sezione Servizio file selezionare Crittografia.

Dopo avere selezionato l'impostazione Crittografia, è possibile abilitare o disabilitare Crittografia del servizio di archiviazione.

![](media/protect-personal-data-at-rest/storage-service-encryption.png)

I nuovi dati verranno crittografati. I dati presenti nei file esistenti in questo account di archiviazione resteranno non crittografati.

Dopo aver abilitato la crittografia, copiare i dati nell'account di archiviazione usando uno dei metodi seguenti:

1. Copiare i BLOB o i file con l'[utilità della riga di comando AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy).

2. [Montare una condivisione di file con SMB](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-windows), per poter usare un'utilità come Robocopy per copiare i file.

3. Copiare dati di BLOB o file da e verso l'archiviazione BLOB o tra account di archiviazione usando [librerie client di archiviazione come .NET](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-blobs).

4.  Usare uno [strumento di esplorazione di archiviazione](https://docs.microsoft.com/azure/storage/storage-explorers) per caricare BLOB nell'account di archiviazione con la crittografia abilitata.

### <a name="transparent-data-encryption"></a>Transparent Data Encryption

Transparent Data Encryption (TDE) è una funzionalità inclusa in SQL Azure che permette di crittografare i dati a livello di database e di server. La tecnologia TDE è ora abilitata per impostazione predefinita in tutti i nuovi database creati. TDE esegue la crittografia e la decrittografia delle operazioni di I/O di file di dati e log in tempo reale.

#### <a name="how-do-i-use-tde-to-protect-personal-data"></a>Come si usa TDE per proteggere i dati personali?

È possibile configurare TDE tramite il portale di Azure, con l'API REST o con PowerShell. Per abilitare TDE in un database esistente usando il portale di Azure, eseguire le operazioni seguenti:

1. Accedere al portale di Azure all'indirizzo <https://portal.azure.com> con l'account di amministratore o collaboratore di Azure.

2. Nel banner a sinistra fare clic su SFOGLIA e quindi su Database SQL.

3. Con Database SQL selezionato nel riquadro a sinistra, fare clic sul database utente.

4. Nel pannello del database fare clic su Tutte le impostazioni.

5. Nel riquadro Impostazioni fare clic sulla parte Transparent Data Encryption per aprire il riquadro Transparent Data Encryption.

6. Nel riquadro Crittografia dati spostare il pulsante Crittografia dati su Sì e quindi fare clic su Salva (nella parte superiore della pagina) per applicare l'impostazione. Lo stato di crittografia indicherà approssimativamente lo stato di Transparent Data Encryption.

![Abilitazione della crittografia dei dati](media/protect-personal-data-at-rest/turn-data-encryption-on.png)

Le istruzioni su come abilitare TDE e le informazioni sulla decrittografia di database protetti con TDE sono disponibili nell'articolo [Transparent Data Encryption con il database SQL di Azure ](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database).

## <a name="summary"></a>Summary

La società può realizzare il proprio obiettivo di crittografare i dati personali archiviati nel cloud di Azure. A questo scopo, può usare Crittografia dischi di Azure per proteggere interi volumi. Possono essere inclusi i file del sistema operativo e i file di dati che contengono informazioni personali e altri dati sensibili. È possibile usare Crittografia del servizio di archiviazione di Azure per proteggere dati personali archiviati in BLOB e file. Per i dati archiviati in database SQL di Azure, Transparent Data Encryption offre protezione dall'esposizione non autorizzata delle informazioni personali.

Per proteggere le chiavi usate per crittografare dati in Azure, la società può usare Azure Key Vault. Questo servizio semplifica il processo di gestione delle chiavi e permette alla società di mantenere il controllo delle chiavi che accedono ai dati personali e li crittografano.

## <a name="next-steps"></a>Passaggi successivi

- [Guida alla risoluzione dei problemi di Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-tsg)

- [Crittografare una macchina virtuale di Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption?toc=%2fazure%2fsecurity%2ftoc.json)

- [Crittografia dei dati in Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-encryption)

- [Crittografia di dati inattivi del database in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)
