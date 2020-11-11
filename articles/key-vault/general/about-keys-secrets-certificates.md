---
title: Panoramica di chiavi, segreti e certificati di Azure Key Vault
description: Panoramica dei dettagli di sviluppo e dell'interfaccia REST di Azure Key Vault per le chiavi, i segreti e i certificati.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 78f228a5e188bc930a9e7484f4c982ba746331dd
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357777"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Panoramica di chiavi, segreti e certificati di Azure Key Vault

Azure Key Vault consente agli utenti e alle applicazioni di Microsoft Azure di archiviare e usare diversi tipi di segreti e chiavi. Il provider di risorse Key Vault supporta due tipi di risorse: gli insiemi di credenziali e i moduli di protezione hardware gestiti.

## <a name="dns-suffixes-for-base-url"></a>Suffissi DNS per l'URL di base
 La tabella seguente mostra il suffisso DNS dell'URL di base usato dall'endpoint del piano dati per gli insiemi di credenziali e i pool di moduli di protezione hardware gestiti in diversi ambienti cloud.

Ambiente cloud | Suffisso DNS per gli insiemi di credenziali | Suffisso DNS per i moduli di protezione hardware gestiti
---|---|---
Cloud di Azure | .vault.azure.net | .managedhsm.azure.net
Cloud di Azure Cina | .vault.azure.cn | Non supportato
Azure US Government | .vault.usgovcloudapi.net | Non supportato
Cloud di Azure per la Germania | .vault.microsoftazure.de | Non supportato
|||


## <a name="object-types"></a>Tipi di oggetto
 La tabella seguente mostra i tipi di oggetto e i rispettivi suffissi nell'URL di base.

Tipo oggetto|Suffisso URL|Insiemi di credenziali|Pool di moduli di protezione hardware gestiti
--|--|--|--
**Chiavi di crittografia**||
Chiavi protette dal modulo di protezione hardware|/keys|Supportato|Supportato
Chiavi protette tramite software|/keys|Supportato|Non supportato
**Altri tipi di oggetto**||
Segreti|/secrets|Supportato|Non supportato
Certificati|/certificates|Supportato|Non supportato
Chiavi dell'account di archiviazione|/storageaccount|Supportato|Non supportato
|||
- **Chiavi crittografiche** : supporta più tipi di chiavi e algoritmi e consente l'uso di chiavi protette tramite software e con protezione HSM. Per altre informazioni, vedere [Informazioni sulle chiavi](../keys/about-keys.md).
- **Segreti** : Offre l'archiviazione sicura di segreti, ad esempio password e stringhe di connessione di database. Per altre informazioni, vedere [Informazioni sui segreti](../secrets/about-secrets.md).
- **Certificati** : Supporta i certificati, basati su chiavi e segreti, e aggiunge una funzionalità di rinnovo automatico. Per altre informazioni, vedere [Informazioni sui certificati](../certificates/about-certificates.md).
- **Chiavi dell'account di archiviazione di Azure** : Può gestire le chiavi di un account di archiviazione di Azure per l'utente. Internamente, Key Vault può elencare (sincronizzare) le chiavi con un account di archiviazione di Azure e rigenerare (ruotare) tali chiavi con cadenza periodica. Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione con Key Vault](../secrets/overview-storage-keys.md).

Per altre informazioni generali su Key Vault, vedere [Informazioni su Azure Key Vault](overview.md). Per altre informazioni sui pool di moduli di protezione hardware gestiti, vedere [Che cos'è il modulo di protezione hardware gestito di Azure Key Vault?](../managed-hsm/overview.md)


## <a name="data-types"></a>Tipi di dati

Fare riferimento alle specifiche JOSE per i tipi di dati pertinenti per chiavi, crittografia e firma.  

-   **algoritmo** : un algoritmo supportato per un'operazione della chiave, ad esempio, RSA1_5  
-   **valore di testo crittografato** : ottetti di testo cifrati, codificati tramite Base64URL  
-   **valore di digest** : l'output di un algoritmo hash, codificato tramite Base64URL  
-   **key-type** : uno dei tipi di chiave supportati, ad esempio RSA (Rivest-Shamir-Adleman)  
-   **valore di testo non crittografato** : ottetti di testo non crittografato, codificati tramite Base64URL  
-   **valore di firma** : l'output di un algoritmo di firma, codificato tramite Base64URL  
-   **base64URL** : un valore binario Base64URL [RFC4648] codificato  
-   **booleano** : vero o falso  
-   **Identità** : un identità di Azure Active Directory (AAD).  
-   **IntDate** : un valore decimale JSON che rappresenta il numero di secondi da 1970-01-01T0:0:0Z UTC fino alla data/ora UTC specificata. Per informazioni dettagliate sui valori data/ora in generale e UTC in particolare, vedere RFC3339.  

## <a name="objects-identifiers-and-versioning"></a>Oggetti, identificatori e controllo delle versioni

Agli oggetti archiviati in Key Vault viene applicato il controllo delle versioni ogni volta che si crea una nuova istanza di un oggetto. A ogni versione vengono assegnati un identificatore univoco e un URL. Un oggetto creato per la prima volta viene etichettato con un identificatore univoco della versione e contrassegnato come versione corrente dell'oggetto. La creazione di una nuova istanza con lo stesso nome assegna al nuovo oggetto un identificatore univoco della versione, trasformando tale oggetto nella versione corrente.  

Gli oggetti in Key Vault possono essere gestiti specificando una versione o omettendola per le operazioni sulla versione corrente dell'oggetto. Ad esempio, data una chiave denominata `MasterKey`, se vengono eseguite operazioni senza specificare una versione il sistema usa l'ultima versione disponibile. L'esecuzione di operazioni con l'identificatore specifico della versione fa sì che il sistema userà quella versione specifica dell'oggetto.  

All'interno di Key Vault gli oggetti sono identificati in modo univoco con un URL. Nel sistema non esistono coppie di oggetti con lo stesso URL, indipendentemente dalla posizione geografica. L'URL completo di un oggetto viene chiamato identificatore di oggetto. L'URL è costituito da un prefisso che identifica l'insieme di credenziali delle chiavi, seguito dal tipo di oggetto, dal nome dell'oggetto specificato dall'utente e dalla versione dell'oggetto. Il nome dell'oggetto non è modificabile e non fa distinzione tra maiuscole e minuscole. Gli identificatori che non includono la versione dell'oggetto vengono definiti identificatori di base.  

Per ulteriori informazioni, vedere[ Autenticazione, richieste e risposte](authentication-requests-and-responses.md)

Un identificatore di oggetto ha il seguente formato generale (in base al tipo di contenitore):  

- **Per gli insiemi di credenziali** : `https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **Per i pool di moduli di protezione hardware gestiti** : `https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> Per informazioni sui tipi di oggetti supportati da ogni tipo di contenitore, vedere [Supporto dei tipi di oggetto](#object-types).

Dove:  

| Elemento | Descrizione |  
|-|-|  
|`vault-name` o `hsm-name`|Nome di un insieme di credenziali o di un pool di moduli di protezione hardware gestiti nel servizio Microsoft Azure Key Vault.<br /><br />I nomi di insiemi di credenziali e di pool di moduli di protezione hardware gestiti vengono selezionati dall'utente e sono univoci a livello globale.<br /><br />Il nome dell'insieme di credenziali e il nome del pool di moduli di protezione hardware gestiti devono essere costituiti da una stringa di lunghezza compresa tra 3 e 24 caratteri, contenente solo i numeri 0-9, i caratteri a-z e A-Z e il trattino -.|  
|`object-type`|Tipo di oggetto, "chiavi", "segreti" o "certificati".|  
|`object-name`|Un `object-name` è un nome utente fornito per un Key Vault e deve essere univoco all'interno di esso. Il nome deve essere costituito da una stringa di lunghezza compresa tra 1 e 127 caratteri, che inizia con una lettera e contiene solo i numeri 0-9, i caratteri a-z e A-Z e il trattino -.|  
|`object-version`|`object-version` è un identificatore di stringa di 32 caratteri generato dal sistema che viene usato facoltativamente per fare riferimento a una versione univoca di un oggetto.|  

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle chiavi](../keys/about-keys.md)
- [Informazioni sui segreti](../secrets/about-secrets.md)
- [Informazioni sui certificati](../certificates/about-certificates.md)
- [Autenticazione, richieste e risposte](../general/authentication-requests-and-responses.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)
