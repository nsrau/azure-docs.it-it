---
title: Metodi di creazione dei certificati
description: Modi per creare un certificato in Key Vault.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e17b4c9b-4ff3-472f-8c9d-d130eb443968
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 7b71c6a8daa97300ecf3b37ec6ab47207fece98e
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
---
# <a name="certificate-creation-methods"></a>Metodi di creazione dei certificati

 Un certificato di Key Vault può essere creato o importato in un insieme di credenziali delle chiavi. Quando si crea un certificato di Key Vault, la chiave privata viene creata all'interno dell'insieme di credenziali delle chiavi e non viene mai esposta al proprietario del certificato. È possibile creare un certificato in Key Vault nei modi seguenti.  

-   **Creare un certificato autofirmato:** in questo modo verrà creata una coppia di chiavi pubblica/privata che verrà associata a un certificato. Il certificato sarà firmato dalla propria chiave.  

-    **Creare un nuovo certificato manualmente:** in questo modo verrà creata una coppia di chiavi pubblica/privata e verrà generata una richiesta di firma del certificato X.509. La richiesta di firma può essere firmata dall'autorità di registrazione o di certificazione. Il certificato x509 firmato può essere unito alla coppia di chiavi in sospeso per completare il certificato in Key Vault. Nonostante richieda più passaggi, questo metodo garantisce maggiore sicurezza perché la chiave privata viene creata in Key Vault ed è limitata a tale ambito, come illustrato nel diagramma seguente.  

![Creare un certificato con un'autorità di certificazione personalizzata](media/certificate-authority-1.png)  

Le descrizioni seguenti corrispondono ai passaggi contrassegnati con un numero in verde nel diagramma precedente.

1. Nel diagramma precedente, l'applicazione crea un certificato, operazione che internamente inizia con la creazione di una chiave nell'insieme di credenziali delle chiavi.
2. Key Vault restituisce all'applicazione una richiesta di firma del certificato.
3. L'applicazione passa la richiesta di firma del certificato alla CA scelta.
4. La CA scelta risponde con un certificato X509.
5. L'applicazione completa la creazione del nuovo certificato con l'unione del certificato X509 proveniente dalla CA.

-   **Creare un certificato con un provider autorità di certificazione noto:** questo metodo richiede un'attività una tantum di creazione di un oggetto autorità di certificazione. Dopo che è stato creato un oggetto autorità di certificazione nell'insieme di credenziali delle chiavi, è possibile fare riferimento al nome di tale oggetto nei criteri del certificato di Key Vault. Una richiesta di creazione di un certificato di Key Vault di questo tipo determinerà la creazione di una coppia di chiavi nell'insieme di credenziali e la comunicazione con il servizio del provider autorità di certificazione usando le informazioni nell'oggetto autorità di certificazione di riferimento per ottenere un certificato x509. Il certificato x509 viene recuperato dal servizio dell'autorità di certificazione e viene unito alla coppia di chiavi per completare la creazione del certificato di Key Vault.  

![Creare un certificato con un'autorità di certificazione partner di Key Vault](media/certificate-authority-2.png)  

Le descrizioni seguenti corrispondono ai passaggi contrassegnati con un numero in verde nel diagramma precedente.

1. Nel diagramma precedente, l'applicazione crea un certificato, operazione che internamente inizia con la creazione di una chiave nell'insieme di credenziali delle chiavi.
2. Key Vault invia una richiesta di certificato SSL alla CA.
3. L'applicazione esegue il polling di Key Vault, in un processo di ciclo e attesa, per il completamento del certificato. La creazione del certificato è completata quando Key Vault riceve la risposta della CA con il certificato x509.
4. La CA risponde alla richiesta di certificato SSL di Key Vault con un certificato SSL X509.
5. La creazione del nuovo certificato viene completata con l'unione del certificato X509 per la CA.

## <a name="asynchronous-process"></a>Processo asincrono
La creazione dei certificati di Key Vault è un processo asincrono. Questa operazione crea una richiesta di certificato di Key Vault e restituisce il codice di stato HTTP 202 (Accettata). Lo stato della richiesta può essere monitorato eseguendo il polling dell'oggetto in sospeso creato dall'operazione. Nell'intestazione LOCATION viene restituito l'URI completo dell'oggetto in sospeso.  

Quando viene completata una richiesta di creazione di un certificato di Key Vault, lo stato dell'oggetto in sospeso passa da "inProgress" a "completed" e viene creata una nuova versione del certificato di Key Vault, che diventa la versione corrente.  

## <a name="first-creation"></a>Prima creazione
 Quando si crea un certificato di Key Vault per la prima volta, vengono creati anche una chiave e un segreto indirizzabili con lo stesso nome del certificato. Se il nome è già in uso, l'operazione avrà esito negativo con codice di stato HTTP 409 (Conflitto).
La chiave e il segreto indirizzabili ottengono i propri attributi dagli attributi del certificato di Key Vault. La chiave e il segreto indirizzabili creati in questo modo vengono contrassegnati come chiavi e segreti gestiti, la cui durata è gestita da Key Vault. Le chiavi e i segreti gestiti sono di sola lettura. Nota: se un certificato di Key Vault scade o viene disabilitato, la chiave e il segreto corrispondenti diventano inutilizzabili.  

 Alla prima operazione di creazione di un certificato di Key Vault sono necessari i criteri.  È possibile anche specificare i criteri con le operazioni di creazione successive, per sostituire la risorsa criteri. Se non si specificano criteri, per creare una versione successiva del certificato di Key Vault viene usata la risorsa criteri del servizio. Si noti che quando è in corso una richiesta per la creazione di una versione successiva, il certificato di Key Vault corrente e la chiave e il segreto indirizzabili corrispondenti rimangono invariati.  

## <a name="self-issued-certificate"></a>Autocertificazione
 Per creare un'autocertificazione, impostare il nome dell'autorità di certificazione su "Self" nei criteri del certificato, come illustrato nel relativo frammento seguente.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Se il nome dell'autorità di certificazione non è specificato, viene impostato su "Unknown". Quando l'autorità di certificazione è "Unknown", il proprietario del certificato dovrà ottenere manualmente un certificato x509 dall'autorità di certificazione di propria scelta e quindi unire il certificato x509 pubblico all'oggetto in sospeso del certificato dell'insieme di credenziali delle chiavi per completare la creazione del certificato.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Provider CA partner
La creazione del certificato può essere completata manualmente oppure usando un'autorità di certificazione "Self". Per semplificare la creazione di certificati, Key Vault collabora anche con alcuni provider autorità di certificazione. Presso questi provider autorità di certificazione partner è possibile ordinare i tipi seguenti di certificati per l'insieme di credenziali delle chiavi.  

|Provider|Tipo di certificato|  
|--------------|----------------------|  
|DigiCert|Key Vault offre certificati SSL OV o EV con DigiCert|
|GlobalCert|Key Vault offre certificati SSL OV o EV con GlobalSign|

 Per altre informazioni, inclusa la disponibilità a livello geografico di questi provider autorità di certificazione, vedere [Certificate Issuers](/rest/api/keyvault/certificate-issuers.md) (Autorità di certificazione).

Si noti che quando viene effettuato un ordine presso il provider autorità di certificazione, le estensioni e il periodo di validità del certificato x509 possono essere rispettati o sostituiti a seconda del tipo di certificato.  

 A livello di autorizzazione, è necessaria l'autorizzazione a creare certificati.

 ## <a name="see-also"></a>Vedere anche
 - [Informazioni su chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
 - [Monitorare e gestire la creazione dei certificati](create-certificate-scenarios.md)
