---
title: Informazioni sui segreti di Azure Key Vault - Azure Key Vault
description: Panoramica dei dettagli di sviluppo e dell'interfaccia REST di Azure Key Vault per i segreti.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 7aa2feba5a2b2fa47bbb0c055a2f556b8997ab34
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "82930472"
---
# <a name="about-azure-key-vault-secrets"></a>Informazioni sui segreti di Azure Key Vault

Key Vault offre l'archiviazione sicura dei segreti, ad esempio password e stringhe di connessione di database.

Dal punto di vista dello sviluppo, le API di Key Vault accettano e restituiscono i valori dei segreti sotto forma di stringhe. Internamente, Key Vault archivia e gestisce i segreti come sequenze di ottetti (byte a 8 bit), con dimensioni massime di 25 kbyte ciascuno. Il servizio Key Vault non fornisce la semantica per i segreti. Accetta semplicemente i dati, li crittografa, li archivia e restituisce un identificatore di segreto ("id"). L'identificatore può essere usato per recuperare il segreto in un secondo momento.  

Per i dati altamente sensibili è opportuno prendere in considerazione livelli di protezione aggiuntivi, ad esempio la crittografia dei dati con una chiave di protezione separata prima dell'archiviazione in Key Vault.  

Key Vault supporta anche un campo contentType per i segreti. I client possono specificare il tipo di contenuto di un segreto per facilitare l'interpretazione dei dati del segreto quando vengono recuperati. Il campo può contenere al massimo 255 caratteri. Non sono presenti valori predefiniti. L'utilizzo consigliato è come un hint per l'interpretazione dei dati segreti. Ad esempio, in un'implementazione possono essere archiviati password e certificati come segreti. Questo campo consente quindi di distinguerli. Non sono presenti valori predefiniti.  

## <a name="encryption"></a>Crittografia

Tutti i segreti presenti in Key Vault vengono archiviati crittografati. Questa crittografia è trasparente e non richiede alcuna azione da parte dell'utente. Il servizio Azure Key Vault crittografa i segreti quando vengono aggiunti e li decrittografa automaticamente quando vengono letti. La chiave di crittografia è univoca per ogni insieme di credenziali delle chiavi.

## <a name="secret-attributes"></a>Attributi dei segreti

Oltre ai dati dei segreti,è possibile specificare gli attributi seguenti:  

- *exp*: il valore predefinito IntDate facoltativo è **forever**. L’attributo *exp* (ora di scadenza) identifica l'ora di scadenza in cui o successiva alla quale i dati dei segreti NON DEVONO essere recuperati, tranne che in [situazioni particolari](#date-time-controlled-operations). Il campo è a solo scopo **informativo** e informa gli utenti del servizio di insieme di credenziali delle chiavi che non è possibile usare un segreto specifico. Il valore DEVE essere un numero contenente un valore IntDate.   
- *nbf*: il valore predefinito IntDate facoltativo è **now**. L’attributo *nbf* (non precedente) identifica l'ora precedente alla quale i dati dei segreti NON DEVONO essere recuperati, tranne che in [situazioni particolari](#date-time-controlled-operations). Questo documento è esclusivamente a scopo **informativo**. Il valore DEVE essere un numero contenente un valore IntDate. 
- *abilitato*: il valore predefinito booleano facoltativo è **vero**. Questo attributo specifica se i dati dei segreti possono essere recuperati. L'attributo abilitato viene usato in combinazione con *nbf* ed *exp* quando si verifica un'operazione tra *nbf* ed *exp*, sarà consentito solo se Abilitato è impostato su **true**. Le operazioni all'esterno della finestra *nbf* ed *exp* non sono impostate automaticamente non consentiti, ad eccezione di [situazioni particolari](#date-time-controlled-operations).  

Sono disponibili altri attributi di sola lettura che sono inclusi in una risposta che include gli attributi dei segreti:  

- *created*: IntDate, facoltativo. L’attributo creato indica quando è stata creata questa versione del segreto. Questo valore è null per i segreti creati prima dell'aggiunta di questo attributo. Il valore deve essere un numero contenente un valore IntDate.  
- *updated*: IntDate, facoltativo. L’attributo aggiornato indica quando è stata aggiornata questa versione del segreto. Questo valore è null peri segreti aggiornati prima dell'aggiunta di questo attributo. Il valore deve essere un numero contenente un valore IntDate.

### <a name="date-time-controlled-operations"></a>Operazioni controllate in base a data e ora

L’operazione **ottieni** di un segreto funzionerà per i segreti non ancora validi e scaduti, all'esterno della finestra *nbf* / *exp*. La chiamata dell’operazione **ottieni** di un segreto, per una chiave privata non ancora valida, può essere utilizzata per scopi di test. Il recupero (**get**) di un segreto scaduto può essere usato per le operazioni di ripristino.

## <a name="secret-access-control"></a>Controllo di accesso per i segreti

Il controllo di accesso per i segreti gestiti in Key Vault viene fornito al livello dell'insieme di credenziali delle chiavi che contiene tali segreti. I criteri di controllo di accesso per i segreti sono distinti dai criteri di controllo di accesso per le chiavi presenti nello stesso insieme di credenziali delle chiavi. Gli utenti possono creare uno o più insiemi di credenziali per i segreti e sono tenuti a mantenere una segmentazione e una gestione dei segreti appropriate in base allo scenario.   

Le autorizzazioni seguenti sono utilizzabili, su base principale, nella voce di controllo di accesso dei segreti in un insieme di credenziali e riflettono fedelmente le operazioni consentite su un oggetto segreto:  

- Autorizzazioni per le operazioni di gestione dei segreti
  - *get*: legge un segreto  
  - *list*: consente di elencare i segreti o le versioni di un segreto archiviati in un Key Vault  
  - *set*: Creare un segreto  
  - *delete*: consente di eliminare un segreto  
  - *recover*: consente di recuperare un server eliminato
  - *backup*: consente di eseguire il backup di un segreto in un insieme di credenziali delle chiavi
  - *restore*: consente di ripristinare un segreto sottoposto a backup in un insieme di credenziali delle chiavi

- Autorizzazioni per le operazioni privilegiate
  - *purge*: consente di eliminare definitivamente un segreto eliminato

Per altre informazioni sull'uso dei segreti, vedere le [operazioni relative ai segreti nell'articolo di riferimento all'API REST di Key Vault](/rest/api/keyvault). Per informazioni sulla definizione delle autorizzazioni, vedere [Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate) (Insiemi di credenziali - Create o Update) e [Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy) (Insiemi di credenziali - Update Access Policy). 

## <a name="secret-tags"></a>Tag dei segreti  
È possibile specificare metadati aggiuntivi specifici dell'applicazione sotto forma di tag. Key Vault supporta fino a 15 tag, ognuno dei quali può avere un nome di 256 caratteri e un valore di 256 caratteri.  

>[!Note]
>Un chiamante può leggere i tag se ha l'autorizzazione *list* o *get*.

## <a name="azure-storage-account-key-management"></a>Gestione delle chiavi dell'account di archiviazione di Azure

Key Vault può gestire le chiavi dell'account di archiviazione Azure:

- Internamente, Key Vault può elencare (sincronizzare) le chiavi con un account di archiviazione di Azure. 
- Key Vault rigenera (ruota) le chiavi con cadenza periodica.
- I valori di chiave non vengono mai restituiti in risposta al chiamante.
- Key Vault gestisce le chiavi sia degli account di archiviazione sia degli account di archiviazione classici.

Per altre informazioni, vedere [Gestire le chiavi dell'account di archiviazione con Azure Key Vault](../secrets/overview-storage-keys.md).

## <a name="storage-account-access-control"></a>Controllo di accesso dell'account di archiviazione

Le autorizzazioni seguenti consentono di autorizzare un utente o un'entità di sicurezza dell'applicazione a eseguire operazioni su un account di archiviazione gestito:  

- Autorizzazioni per le operazioni relative all'account di archiviazione gestito e alla definizione SAS
  - *get*: consente di ottenere informazioni su un account di archiviazione 
  - *list*: consente di elencare gli account di archiviazione gestiti da Key Vault
  - *update*: consente di creare un account di archiviazione
  - *delete*: Eliminare un account di archiviazione  
  - *recover*: consente di ripristinare un account di archiviazione eliminato
  - *backup*: consente di eseguire il backup di un account di archiviazione
  - *restore*: consente di ripristinare un account di archiviazione sottoposto a backup in un Key Vault
  - *set*: consente di creare o aggiornare un account di archiviazione
  - *regeneratekey*: consente di rigenerare un valore di chiave specificato per un account di archiviazione
  - *getsas*: consente di ottenere informazioni su una definizione SAS per un account di archiviazione
  - *listsas*: consente di elencare le definizioni SAS di archiviazione per un account di archiviazione
  - *deletesas*: consente di eliminare una definizione SAS da un account di archiviazione
  - *setsas*: consente di creare o aggiornare una nuova definizione SAS/nuovi attributi per un account di archiviazione

- Autorizzazioni per le operazioni privilegiate
  - *purge*: consente di eliminare definitivamente un account di archiviazione gestito

Per altre informazioni, vedere le operazioni relative ai certificati in [Key Vault REST API reference](/rest/api/keyvault) (Riferimenti sull'API REST di Key Vault). Per informazioni sulla definizione delle autorizzazioni, vedere [Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate) (Insiemi di credenziali - Create o Update) e [Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy) (Insiemi di credenziali - Update Access Policy).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su Key Vault](../general/overview.md)
- [Informazioni su chiavi, segreti e certificati](../general/about-keys-secrets-certificates.md)
- [Informazioni sulle chiavi](../keys/about-keys.md)
- [Informazioni sui certificati](../certificates/about-certificates.md)
- [Autenticazione, richieste e risposte](../general/authentication-requests-and-responses.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)
