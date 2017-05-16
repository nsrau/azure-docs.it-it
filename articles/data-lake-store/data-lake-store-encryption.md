---
title: Crittografia in Azure Data Lake Store | Microsoft Docs
description: Informazioni sul funzionamento della crittografia e della rotazione delle chiavi in Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 9bd9996a4a22b2f57510b6f3b6625e22b3183b1c
ms.contentlocale: it-it
ms.lasthandoff: 04/29/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Crittografia dei dati in Azure Data Lake Store

## <a name="overview-of-encryption-in-azure-data-lake-store"></a>Panoramica della crittografia in Azure Data Lake Store

La crittografia in Azure Data Lake Store (ADLS) consente di proteggere i dati, implementare criteri di sicurezza aziendali e soddisfare i requisiti di conformità normativi. Questo articolo offre una panoramica della progettazione e illustra gli aspetti tecnici dell'implementazione della crittografia dei dati con Data Lake Store.

ADLS supporta la crittografia dei dati inattivi trasparente e attivata per impostazione predefinita. Di seguito è riportata una spiegazione più dettagliata:

* Attivata per impostazione predefinita: quando si crea un nuovo account Azure Data Lake Store, l'impostazione predefinita consente la crittografia. I dati archiviati in Data Lake Store quindi vengono sempre crittografati prima essere archiviati in supporti persistenti. Questo comportamento è valido per tutti i dati e non può essere modificato dopo che un account è stato creato.
* Trasparente: ADLS crittografa automaticamente i dati prima di renderli persistenti e li decrittografa prima di recuperarli. La crittografia viene configurata e gestita a livello di Data Lake Store da un amministratore. Non vengono apportate modifiche alle API di accesso ai dati e quindi non sono necessarie modifiche nelle applicazioni e nei servizi che interagiscono con Data Lake Store a causa della crittografia.

Anche i dati in transito (noti anche come dati in movimento) vengono sempre crittografati in Data Lake Store. I dati, oltre a essere crittografati prima dell'archiviazione in supporti persistenti, vengono sempre protetti anche mentre sono in transito o in movimento usando HTTPS (HTTP over Secure Sockets Layer). HTTPS è l'unico protocollo supportato per le interfacce REST di Data Lake Store.

![Figura 1](./media/data-lake-store-encryption/fig1.png)


## <a name="setting-up-encryption-with-azure-data-lake-store"></a>Configurazione della crittografia con Azure Data Lake Store

La crittografia per Azure Data Lake Store viene configurata durante la creazione di un account ed è sempre abilitata per impostazione predefinita. I clienti possono scegliere se gestire le chiavi o consentire ad Azure Data Lake Store (impostazione predefinita) di gestirle.

Per informazioni su come configurare la crittografia con Azure Data Lake Store, vedere [Introduzione](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

## <a name="under-the-hood--how-encryption-works-in-azure-data-lake-store"></a>Approfondimento sul funzionamento della crittografia in Azure Data Lake Store

### <a name="master-encryption-keys"></a>Chiavi di crittografia master

Azure Data Lake Store offre due modalità per la gestione delle chiavi di crittografia master. L'utilizzo delle chiavi di crittografia master è illustrato più avanti in modo più approfondito. Per il momento si supponga che la chiave di crittografia sia la chiave principale. L'accesso alla chiave di crittografia master è necessario per decrittografare i dati archiviati in Data Lake Store.

Le due modalità per la gestione della chiavi di crittografia master sono le seguenti:

1.    Chiavi gestite dal servizio
2.    Chiavi gestite dal cliente

In entrambe le modalità la chiave di crittografia master viene protetta archiviandola in Azure Key Vault. Azure Key Vault è un servizio di Azure completamente gestito a sicurezza elevata che può essere usato per proteggere le chiavi crittografiche. Per altre informazioni su Azure Key Vault, vedere [qui](https://azure.microsoft.com/services/key-vault).

Ecco un breve confronto delle funzionalità offerte dalle due modalità di gestione delle chiavi di crittografia master.

|  | Chiavi gestite dal servizio | Chiavi gestite dal cliente |
| --- | --- | --- |
|Come vengono archiviati i dati?|Vengono sempre crittografati prima dell'archiviazione|Vengono sempre crittografati prima dell'archiviazione|
|Dove viene archiviata la chiave di crittografia master?|Azure Key Vault|Azure Key Vault|
|Vengono archiviate chiavi di crittografia in chiaro al di fuori di Azure Key Vault?|No|No|
|La chiave di crittografia master può essere recuperata da Azure Key Vault?|No. Dopo essere stata archiviata nell'insieme di credenziali delle chiavi, può solo essere usata per la crittografia e la decrittografia.|No. Dopo essere stata archiviata nell'insieme di credenziali delle chiavi, può solo essere usata per la crittografia e la decrittografia.|
|Chi è il proprietario dell'istanza di Azure Key Vault e della chiave di crittografia master?|Il servizio Azure Data Lake Store.|Il cliente è il proprietario dell'istanza di Azure Key Vault, che appartiene alla sottoscrizione di Azure. La chiave di crittografia master nell'insieme di credenziali delle chiavi può essere gestita dal software o dall'hardware (modulo di protezione hardware).|
|Il cliente può revocare l'accesso alla chiave di crittografia master per il servizio Azure Data Lake Store?|No|Sì. Può gestire gli elenchi di controllo di accesso in Azure Key Vault e rimuovere le voci di controllo di accesso nell'identità del servizio per il servizio Azure Data Lake Store.|
|Il cliente può eliminare definitivamente la chiave di crittografia master?|No|Sì. Se il cliente elimina la chiave di crittografia master da Azure Key Vault, i dati nell'account ADLS non possono essere in alcun modo decrittografati, neppure dal servizio Azure Data Lake Store. <br><br> Se il cliente esegue un backup esplicito della chiave di crittografia master prima di eliminarla da Azure Key Vault, la chiave può essere ripristinata e i dati recuperati. Se tuttavia il cliente non esegue un backup della chiave di crittografia master prima di eliminarla da Azure Key Vault, i dati nell'account ADLS non potranno più essere decrittografati.|


Fatta eccezione per la differenza principale, ovvero chi gestisce la chiave di crittografia master e l'insieme di credenziali delle chiavi in cui si trova, la progettazione è la medesima per entrambe le modalità.

Per scegliere la modalità per le chiavi di crittografia master, è opportuno tenere presenti alcuni importanti aspetti.

1.    È possibile scegliere se usare chiavi gestite dal cliente o da ADLS quando si effettua il provisioning di un account ADLS
2.    Dopo il provisioning di un account ADLS, non è possibile modificare la modalità

### <a name="encryption-and-decryption-of-data"></a>Crittografia e decrittografia dei dati

Nella progettazione della crittografia dei dati per Azure Data Lake vengono usati tre tipi di chiavi. La tabella seguente ne riepiloga l'uso:

| Chiave                   | Abbreviazione | Associata a | Posizione di archiviazione                             | Tipo       | Note                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Chiave di crittografia master | MEK          | Account ADLS | Azure Key Vault                              | Asimmetrica | Può essere gestita da ADLS o dal cliente                                                              |
| Chiave di crittografia dei dati   | DEK          | Account ADLS | Risorsa di archiviazione persistente, gestita dal servizio ADLS | Simmetrica  | La chiave di crittografia dei dati viene crittografata dalla chiave di crittografia master ed è la chiave di crittografia dei dati crittografata a essere archiviata nei supporti persistenti dal servizio |
| Chiave di crittografia a blocchi  | BEK          | Un blocco di dati | Nessuno                                         | Simmetrica  | La chiave di crittografia a blocchi deriva dalla chiave di crittografia dei dati e dal blocco di dati                                                      |

Il diagramma seguente illustra questi concetti:

![Figura 2](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudoalgoritmo quando un file deve essere decrittografato:
1.    Controllare se la chiave di crittografia dei dati per l'account ADLS è memorizzata nella cache e può essere usata.
    * In caso contrario, leggere la chiave di crittografia dei dati crittografata dalla risorsa di archiviazione persistente e inviarla ad Azure Key Vault per la decrittografia. Memorizzare nella cache la chiave di crittografia dei dati decrittografata che potrà quindi essere usata.
2.    Per ogni blocco di dati nel file:
    * Leggere il blocco di dati crittografato da una risorsa di archiviazione persistente
    * Generare la chiave di crittografia a blocchi dalla chiave di crittografia dei dati e dal blocco di dati crittografato
    * Usare la chiave di crittografia a blocchi per decrittografare i dati
#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudoalgoritmo quando un file deve essere crittografato:
1.    Controllare se la chiave di crittografia dei dati per l'account ADLS è memorizzata nella cache e può essere usata.
    * In caso contrario, leggere la chiave di crittografia dei dati crittografata dalla risorsa di archiviazione persistente e inviarla ad Azure Key Vault per la decrittografia. Memorizzare nella cache la chiave di crittografia dei dati decrittografata che potrà quindi essere usata.
2.    Generare una chiave di crittografia a blocchi univoca per il blocco di dati dalla chiave di crittografia dei dati.
3.    Crittografare il blocco di dati con la chiave di crittografia a blocchi usando la crittografia AES-256.
4.    Archiviare il blocco di dati crittografato in una risorsa di archiviazione persistente

> [!NOTE] 
> Per motivi di prestazioni, la chiave di crittografia dei dati in chiaro viene memorizzata nella cache per un breve intervallo di tempo e immediatamente cancellata al termine di questo intervallo. Nei supporti persistenti viene sempre archiviata dopo essere stata crittografata dalla chiave di crittografia master.

## <a name="key-rotation"></a>Rotazione delle chiavi

Azure Data Lake Store consente la rotazione della chiave di crittografia master quando si usano chiavi gestite dal cliente. Per informazioni su come configurare un account ADLS con chiavi gestite dal cliente, vedere la pagina [Introduzione](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="pre-requisites"></a>Prerequisiti

Durante la configurazione dell'account Azure Data Lake, i clienti hanno scelto di usare le proprie chiavi. Questa opzione non può essere modificata dopo la creazione dell'account. Se si usano le opzioni predefinite per la crittografia, i dati verranno sempre crittografati usando le chiavi gestite da Azure Data Lake. Con questa opzione il cliente non ha la possibilità di ruotare le chiavi perché sono gestite da Azure Data Lake. I passaggi seguenti presuppongono che si usino chiavi gestite dal cliente. Scegliere le chiavi dal proprio insieme di credenziali delle chiavi.

### <a name="how-to-rotate-the-key-mek-in-azure-data-lake-store"></a>Come ruotare la chiave di crittografia master in Azure Data Lake Store

1. Accedere al nuovo [portale di Azure](https://portal.azure.com/).
2. Passare all'insieme di credenziali delle chiavi in cui sono archiviate le chiavi associate all'account Azure Data Lake Store e selezionare le chiavi.

    ![Chiavi](./media/data-lake-store-encryption/keyvault.png)

3.    Selezionare la chiave associata all'account Azure Data Lake Store e creare una nuova versione di questa chiave.
  
   Azure Data Lake attualmente supporta solo la Rotazione delle chiavi a una nuova versione di una chiave, ma non la rotazione a una chiave diversa.

   ![Nuova versione](./media/data-lake-store-encryption/keynewversion.png)

4.    Passare all'account di archiviazione di Azure Data Lake e selezionare Crittografia

    ![Nuova versione](./media/data-lake-store-encryption/select-encryption.png)

5.    Verranno visualizzati una nota che informa che è disponibile una nuova versione della chiave e un pulsante per ruotare la chiave a questa nuova versione. Fare clic su Ruota chiave per aggiornare la chiave alla nuova versione.

    ![Operazione completata](./media/data-lake-store-encryption/rotatekey.png)

6. Questa operazione richiederà meno di 2 minuti e non sono previsti tempi di inattività a causa della rotazione della chiave. Al termine dell'operazione, sarà in uso la nuova versione della chiave.

