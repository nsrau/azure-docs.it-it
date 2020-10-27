---
title: Chiavi gestite dal cliente o Bring Your Own Key (BYOK) con Servizi multimediali
description: Questa esercitazione illustra come usare le chiavi gestite dal cliente con un account di archiviazione di Servizi multimediali
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c26da9d888bbcdf72c052fa4bd7fcdf443a2d8f7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325861"
---
# <a name="tutorial-use-customer-managed-keys-or-bring-your-own-key-byok-with-media-services"></a>Esercitazione: Usare chiavi gestite dal cliente o Bring Your Own Key (BYOK) con Servizi multimediali

Con l'API 2020-05-01 è possibile usare una chiave RSA gestita dal cliente con un account di Servizi multimediali che ha un'identità gestita dal sistema.  Questa esercitazione include una raccolta e un ambiente Postman per l'invio di richieste REST ai servizi di Azure.  I servizi usati sono:

- Registrazione dell'applicazione Azure Active Directory per Postman
- API Microsoft Graph
- Archiviazione di Azure
- Insieme di credenziali chiave di Azure
- Servizi multimediali

In questa esercitazione si apprenderà come usare Postman per:

> [!div class="checklist"]
> * Ottenere i token da usare con i servizi di Azure.
> * Creare un gruppo di risorse e un account di archiviazione.
> * Creare un account di Servizi multimediali con un'identità gestita dal sistema.
> * Creare un insieme di credenziali delle chiavi in cui archiviare una chiave RSA (gestita dal cliente) da usare con l'account di archiviazione.
> * Aggiornare l'account di Servizi multimediali per l'uso della chiave RSA con l'account di archiviazione.
> * Usare variabili in Postman.

Se non si ha una sottoscrizione di Azure, creare un [account per una versione di prova gratuita](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- Registrare un'entità servizio con le autorizzazioni appropriate.
- Installare [Postman](https://www.postman.com).
- Scaricare la raccolta di Postman per questa esercitazione dalla pagina [Azure Samples: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registrare un'entità servizio con le autorizzazioni necessarie

[Creare un'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).  Vedere [Opzione 2](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) per informazioni su come ottenere il segreto dell'entità servizio.  Prendere nota del segreto, in quanto non sarà più accessibile una volta usciti dalla relativa pagina nel portale.

Per poter eseguire le attività di questa esercitazione, l'entità servizio deve avere le autorizzazioni seguenti.

![Autorizzazioni necessarie per l'entità servizio](./media/tutorial-byok/service-principal-permissions-1.png)

### <a name="install-postman"></a>Installare Postman

Se Postman non è ancora stato installato per l'uso con Azure, è possibile ottenerlo all'indirizzo [postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Scaricare e importare la raccolta

Scaricare la raccolta di Postman per questa esercitazione dalla pagina [Azure Samples: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

## <a name="installation-of-collection-and-environment"></a>Installazione della raccolta e dell'ambiente

1. Eseguire Postman.
1. Selezionare **Importa** .
1. Selezionare **Upload files** (Carica file).
1. Passare alla posizione in cui sono stati salvati i file della raccolta e dell'ambiente.
1. Selezionare i file della raccolta e dell'ambiente.
1. Selezionare **Open** (Apri).  Verrà visualizzato un avviso che informa che i file non verranno importati come API, ma come raccolte.  È corretto.  È il risultato che si vuole ottenere.
1. La raccolta verrà ora visualizzata nell'elenco delle raccolte come BYOK.
1. Le variabili di ambiente verranno visualizzate nell'elenco degli ambienti.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Informazioni sulle richieste di API REST nella raccolta

La raccolta fornisce le richieste di API REST seguenti. Le richieste devono essere inviate nella sequenza specificata, poiché la maggior parte di esse contiene script di test che creano dinamicamente variabili globali per la richiesta successiva nella sequenza. Non è necessario creare manualmente le variabili globali.

In Postman queste variabili sono racchiuse tra parentesi graffe `{{ }}`.  Ad esempio, `{{bearerToken}}`

1. Ottenere il token di AAD - il test imposta la variabile globale *bearerToken*
2. Ottenere il token di Graph - il test imposta la variabile globale *graphToken*
3. Ottenere i dettagli dell'entità servizio - il test imposta la variabile globale *servicePrincipalObjectId*
4. Creare un account di archiviazione - il test imposta la variabile globale *storageAccountId*
5. Creare un account di Servizi multimediali con un'identità gestita dal sistema - il test imposta la variabile globale *principalId*
6. Creare un insieme di credenziali delle chiavi, concedendo l'accesso all'entità servizio - il test imposta la variabile globale *keyVaultId*
7. Ottenere un token di Key Vault - il test imposta la variabile globale *keyVaultToken*
8. Creare una chiave RSA nell'insieme di credenziali delle chiavi - il test imposta la variabile globale *keyId*
9. Aggiornare l'account di Servizi multimediali per l'uso della chiave con l'account di archiviazione - non è disponibile uno script di test per questa richiesta.

## <a name="define-environment-variables"></a>Definire le variabili di ambiente

1. Passare all'ambiente scaricato selezionandolo dall'elenco a discesa degli ambienti.
1. Definire le variabili di ambiente in Postman. Anche queste variabili sono racchiuse tra parentesi graffe `{{ }}`.  Ad esempio, `{{tenantId}}`

    * *tenantId* = ID del tenant
    * *servicePrincipalId* = ID dell'entità servizio stabilita con il metodo preferito: portale, interfaccia della riga di comando o altro.
    * *servicePrincipalSecret* = segreto creato per l'entità servizio
    * *subscription* = ID della sottoscrizione
    * *storageName* = nome da assegnare all'archiviazione
    * *accountName* = nome dell'account di Servizi multimediali da usare
    * *keyVaultName* = nome dell'insieme di credenziali delle chiavi da usare
    * *resourceLocation* = Stati Uniti centrali (o ovunque si vogliano inserire le risorse.  Questa raccolta è stata testata solo con la località Stati Uniti centrali)
    * *resourceGroup* = nome del gruppo di risorse

    Le variabili seguenti sono standard per l'uso delle risorse di Azure, quindi non è necessario modificarle.

    * *armResource* = `https://management.core.windows.net`
    * *graphResource* = `https://graph.windows.net/`
    * *keyVaultResource* = `https://vault.azure.net`
    * *armEndpoint* = `management.azure.com`
    * *graphEndpoint* = `graph.windows.net`
    * *aadEndpoint* = `login.microsoftonline.com`
    * *keyVaultDomainSuffix* = `vault.azure.net`

## <a name="send-the-requests"></a>Inviare le richieste

Una volta definite le variabili di ambiente, è possibile eseguire le richieste una alla volta nella sequenza indicata sopra oppure usare lo strumento di esecuzione di Postman per eseguire la raccolta.

## <a name="change-the-key"></a>Modificare la chiave

Servizi multimediali rileva automaticamente se la chiave è stata modificata.  Per verificarlo, creare un'altra versione di chiave per la stessa chiave. Servizi multimediali dovrebbe rilevare questa chiave in meno di 15 minuti.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare le risorse create e **si vuole quindi interromperne la fatturazione** , eliminarle.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come...
> [!div class="nextstepaction"]
> [Codificare un file remoto basato su URL ed eseguire lo streaming del video con REST](stream-files-tutorial-with-rest.md)
