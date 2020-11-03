---
title: Usare chiavi gestite dal cliente o BYOK
description: In questa esercitazione viene illustrato come usare chiavi gestite dal cliente o BYOK (Bring Your Own Key) con un account di archiviazione di Servizi multimediali di Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 010e5b8fc394448840f8ff8fd11e92ca2e1ec9d4
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740468"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services"></a>Esercitazione: Usare chiavi gestite dal cliente o BYOK con Servizi multimediali

Con l'API 2020-05-01 è possibile usare una chiave RSA gestita dal cliente con un account di Servizi multimediali di Azure che ha un'identità gestita dal sistema. Questa esercitazione include una raccolta e un ambiente Postman per l'invio di richieste REST ai servizi di Azure. I servizi usati sono:

- Registrazione dell'applicazione Azure Active Directory (Azure AD) per Postman
- API Microsoft Graph
- Archiviazione di Azure
- Insieme di credenziali chiave di Azure
- Servizi multimediali di Azure

In questa esercitazione si apprenderà come usare Postman per:

> [!div class="checklist"]
> - Ottenere i token da usare con i servizi di Azure.
> - Creare un gruppo di risorse e un account di archiviazione.
> - Creare un account di Servizi multimediali con un'identità gestita dal sistema.
> - Creare un insieme di credenziali delle chiavi in cui archiviare una chiave RSA gestita dal cliente.
> - Aggiornare l'account di Servizi multimediali per l'uso della chiave RSA con l'account di archiviazione.
> - Usare variabili in Postman.

Se non si ha una sottoscrizione di Azure, creare un [account per una versione di prova gratuita](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

1. Registrare un'entità servizio con le autorizzazioni appropriate.
1. Installare [Postman](https://www.postman.com).
1. Scaricare la raccolta di Postman per questa esercitazione dalla pagina [Azure Samples: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registrare un'entità servizio con le autorizzazioni necessarie

1. [Creare un'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
1. Passare a [Opzione 2: creare un nuovo segreto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) per ottenere il segreto dell'entità servizio.

   > [!IMPORTANT]
   >Copiare e salvare il valore del segreto per un uso successivo. Il segreto non sarà infatti più accessibile una volta usciti dalla relativa pagina nel portale.

1. Assegnare le autorizzazioni all'entità servizio, come illustrato nello screenshot seguente:

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="Screenshot che mostra le autorizzazioni necessarie per l'entità servizio.":::
   Le autorizzazioni sono elencate per servizio, nome dell'autorizzazione, tipo e quindi descrizione. Azure Key Vault: rappresentazione utente, delegato, accesso completo ad Azure Key Vault. Gestione dei servizi di Azure: rappresentazione utente, delegato, accesso alla gestione dei servizi di Azure come utente dell'organizzazione. Archiviazione di Azure: rappresentazione utente, delegato, accesso ad Archiviazione di Azure. Servizi multimediali: rappresentazione utente, delegato, accesso a Servizi multimediali. Microsoft Graph: user.read, delegato, accesso e lettura del profilo utente.
   :::image-end:::

### <a name="install-postman"></a>Installare Postman

Se Postman non è ancora stato installato per l'uso con Azure, è possibile ottenerlo all'indirizzo [postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Scaricare e importare la raccolta

Scaricare la raccolta di Postman per questa esercitazione dalla pagina [Azure Samples: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

## <a name="install-the-postman-collection-and-environment"></a>Installare la raccolta e l'ambiente Postman

1. Eseguire Postman.
1. Selezionare **Importa**.
1. Selezionare **Upload files** (Carica file).
1. Passare alla posizione in cui sono stati salvati i file della raccolta e dell'ambiente.
1. Selezionare i file della raccolta e dell'ambiente.
1. Selezionare **Open** (Apri). Verrà visualizzato un avviso che informa che i file non verranno importati come API, ma come raccolte. L'avviso è corretto. È il risultato che si vuole ottenere.

La raccolta viene ora visualizzata nell'elenco delle raccolte come BYOK. Le variabili di ambiente vengono inoltre visualizzate nell'elenco degli ambienti.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Informazioni sulle richieste di API REST nella raccolta

La raccolta fornisce le richieste di API REST seguenti.

> [!NOTE]
>
>- Le richieste devono essere inviate nella sequenza specificata.
>- Alla maggior parte delle richieste sono applicati script di test che creano dinamicamente variabili globali per la richiesta successiva nella sequenza.
>- Non occorre creare manualmente le variabili globali.

In Postman queste variabili sono racchiuse tra parentesi graffe. Ad esempio `{{bearerToken}}`.

1. Ottenere un token di Azure AD: il test imposta la variabile globale **bearerToken**.
2. Ottenere un token di Microsoft Graph: il test imposta la variabile globale **graphToken**.
3. Ottenere i dettagli dell'entità servizio: il test imposta la variabile globale **servicePrincipalObjectId**.
4. Creare un account di archiviazione: il test imposta la variabile globale **storageAccountId**.
5. Creare un account di Servizi multimediali con un'identità gestita dal sistema: il test imposta la variabile globale **principalId**.
6. Creare un insieme di credenziali delle chiavi per concedere l'accesso all'entità servizio: il test imposta la variabile globale **keyVaultId**.
7. Ottenere un token di Key Vault: il test imposta la variabile globale **keyVaultToken**.
8. Creare la chiave RSA nell'insieme di credenziali delle chiavi: il test imposta la variabile globale **keyId**.
9. Aggiornare l'account di Servizi multimediali per l'uso della chiave con l'account di archiviazione: non è disponibile uno script di test per questa richiesta.

## <a name="define-environment-variables"></a>Definire le variabili di ambiente

1. Selezionare l'elenco a discesa degli ambienti per passare all'ambiente scaricato.
1. Definire le variabili di ambiente in Postman. Anche queste variabili sono racchiuse tra parentesi graffe. Ad esempio `{{tenantId}}`.

    - **tenantId** : ID tenant.
    - **servicePrincipalId** : ID dell'entità servizio stabilita con il metodo preferito, come il portale o l'interfaccia della riga di comando.
    - **servicePrincipalSecret** : segreto creato per l'entità servizio.
    - **Sottoscrizione** : ID sottoscrizione personale.
    - **storageName** : nome da assegnare all'archiviazione.
    - **accountName** : nome dell'account di Servizi multimediali da usare.
    - **keyVaultName** : nome dell'insieme di credenziali delle chiavi da usare.
    - **resourceLocation** : area **Stati Uniti centrali** o l'area in cui si vogliono inserire le risorse. Questa raccolta è stata testata solo con l'area **Stati Uniti centrali**.
    - **resourceGroup** : Il nome del gruppo di risorse.

    Le variabili seguenti sono standard per l'uso delle risorse di Azure, quindi non è necessario modificarle.

    - **armResource** : `https://management.core.windows.net`
    - **graphResource** : `https://graph.windows.net/`
    - **keyVaultResource** : `https://vault.azure.net`
    - **armEndpoint** : `management.azure.com`
    - **graphEndpoint** : `graph.windows.net`
    - **aadEndpoint** : `login.microsoftonline.com`
    - **keyVaultDomainSuffix** : `vault.azure.net`

## <a name="send-the-requests"></a>Inviare le richieste

Dopo aver definito le variabili di ambiente, è possibile eseguire le richieste una alla volta nella sequenza specificata in precedenza. Oppure si può usare lo strumento di esecuzione di Postman per eseguire la raccolta.

## <a name="change-the-key"></a>Modificare la chiave

Servizi multimediali rileva automaticamente quando la chiave viene modificata. Per testare questo processo, creare un'altra versione di chiave per la stessa chiave. Servizi multimediali dovrebbe rilevare la chiave in meno di 15 minuti.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare le risorse create e *si vuole quindi interromperne la fatturazione* , eliminarle.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come:
> [!div class="nextstepaction"]
> [Codificare un file remoto basato su URL ed eseguire lo streaming del video con REST](stream-files-tutorial-with-rest.md)
