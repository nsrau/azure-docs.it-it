---
title: Esercitazione - Personalizzare l'interfaccia utente delle applicazioni in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come personalizzare l'interfaccia utente delle applicazioni in Azure Active Directory B2C mediante il portale di Azure.
services: B2C
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f8ebb282d3f6abbc37739891c0f7228bef110d82
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842680"
---
# <a name="tutorial-customize-the-user-interface-of-your-applications-in-azure-active-directory-b2c"></a>Esercitazione: Personalizzare l'interfaccia utente delle applicazioni in Azure Active Directory B2C

Per esperienze utente più comuni, come l'iscrizione, l'accesso e la modifica del profilo, è possibile usare i [flussi utente](active-directory-b2c-reference-policies.md) di Azure Active Directory (Azure AD) B2C. Le informazioni fornite in questa esercitazione spiegano come [personalizzare l'interfaccia utente](customize-ui-overview.md) di queste esperienze usando i propri file HTML e CSS.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare file di personalizzazione dell'interfaccia utente
> * Creare un flusso utente di iscrizione e di accesso che usa i file
> * Testare l'interfaccia utente personalizzata

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Se non è ancora stato creato un [tenant Azure AD B2C](tutorial-create-tenant.md), crearlo ora. È possibile usare un tenant esistente, se ne è stato creato uno in un'esercitazione precedente.

## <a name="create-customization-files"></a>Creare i file di personalizzazione

Occorre creare un account di archiviazione e un contenitore di Azure e quindi inserire i file HTML e CSS di base nel contenitore.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

I file possono essere archiviati in diversi modi, ma per questa esercitazione si userà l'[archivio BLOB di Azure](../storage/blobs/storage-blobs-introduction.md).

1. Assicurarsi di usare la directory che contiene la sottoscrizione di Azure. Selezionare la **directory e il filtro di sottoscrizione** nel menu in alto e selezionare la directory che contiene la sottoscrizione. Questa directory è diversa da quella che contiene il tenant Azure B2C.

    ![Passare alla directory della sottoscrizione](./media/tutorial-customize-ui/switch-directories.png)

2. Scegliere Tutti i servizi nell'angolo superiore sinistro del portale di Azure e quindi cercare e selezionare **Account di archiviazione**. 
3. Selezionare **Aggiungi**.
4. In **Gruppo di risorse** selezionare **Crea nuovo**, immettere un nome per il nuovo gruppo di risorse e quindi fare clic su **OK**.
5. Immettere il nome dell'account di archiviazione. Il nome deve essere univoco in Azure, deve avere una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
6. Selezionare il percorso dell'account di archiviazione o accettare quello predefinito. 
7. Accettare tutti gli altri valori predefiniti, selezionare **Rivedi e crea** e fare clic su **Crea**.
8. Dopo aver creato l'account di archiviazione, selezionare **Vai alla risorsa**.

### <a name="create-a-container"></a>Creare un contenitore

1. Nella pagina Panoramica dell'account di archiviazione selezionare **BLOB**.
2. Selezionare **Contenitore**, immettere un nome per il contenitore, selezionare **BLOB (accesso in lettura anonimo solo per i BLOB)** e quindi fare clic su **OK**.

### <a name="enable-cors"></a>Abilitare CORS

 Il codice Azure AD B2C in un browser usa un approccio moderno e standard per caricare il contenuto personalizzato da un URL specificato in un flusso utente. La funzionalità Condivisione di risorse tra le origini (CORS) abilita la richiesta di un numero limitato di risorse in una pagina Web da parte di altri domini.

1. Nel menu selezionare **CORS**.
2. Per **Origini consentite** immettere `https://your-tenant-name.b2clogin.com`. Sostituire `your-tenant-name` con il nome del tenant di Azure AD B2C. Ad esempio: `https://fabrikam.b2clogin.com`. È necessario usare solo lettere minuscole quando si immette il nome del tenant.
3. Per **Metodi consentiti** selezionare sia `GET` che `OPTIONS`.
4. Per **Intestazioni consentite** immettere un asterisco (*).
5. Per **Intestazioni esposte** immettere un asterisco (*).
6. Per **Età massima** immettere 200.

    ![Abilitare CORS](./media/tutorial-customize-ui/enable-cors.png)

5. Fare clic su **Save**.

### <a name="create-the-customization-files"></a>Creare i file di personalizzazione

Per personalizzare l'interfaccia utente dell'esperienza di iscrizione, creare innanzitutto un file HTML e un file CSS semplici. È possibile configurare l'HTML nel modo che si preferisce, purché abbia un elemento **div** con un identificatore `api`. Ad esempio: `<div id="api"></div>`. Azure AD B2C inserisce gli elementi nel contenitore `api` quando viene visualizzata la pagina.

1. In una cartella locale creare il file seguente e sostituire `your-storage-account` con il nome dell'account di archiviazione e `your-container` con il nome del contenitore creato. Ad esempio: `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>  
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    La pagina può essere progettata nel modo che si preferisce, ma l'elemento div **api** è obbligatorio per qualsiasi file di personalizzazione HTML creato. 

3. Salvare il file con il nome *custom-ui.html*.
4. Creare il seguente codice CSS semplice, che allinea tutti gli elementi al centro della pagina di iscrizione o accesso, inclusi gli elementi inseriti da Azure AD B2C.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Salvare il file con il nome *style.css*.

### <a name="upload-the-customization-files"></a>Caricare i file di personalizzazione

In questa esercitazione i file creati devono essere archiviati nell'account di archiviazione, in modo che Azure AD B2C possa accedervi.

1. Scegliere **Tutti i servizi** nell'angolo superiore sinistro del portale di Azure e quindi cercare e selezionare **Account di archiviazione**.
2. Selezionare l'account di archiviazione creato, selezionare **BLOB** e quindi selezionare il contenitore creato.
3. Selezionare **Carica**, individuare e selezionare il file *custom-ui.html* e quindi fare clic su **Carica**.

    ![Caricare i file di personalizzazione](./media/tutorial-customize-ui/upload-blob.png)

4. Copiare l'URL del file che era stato caricato per usarlo successivamente nell'esercitazione.
5. Ripetere i passaggi 3 e 4 per il file *style.css*.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Creare un flusso utente di iscrizione e accesso

Per completare le procedure di questa esercitazione è necessario creare un'applicazione di test e un flusso utente di iscrizione o accesso in Azure AD B2C. È possibile applicare i principi descritti in questa esercitazione alle altre esperienze utente, come la modifica del profilo.

### <a name="create-an-azure-ad-b2c-application"></a>Creare un'applicazione Azure AD B2C

La comunicazione con Azure AD B2C avviene tramite un'applicazione creata nel tenant. La procedura seguente crea un'applicazione che reindirizza il token di autorizzazione restituito a [https://jwt.ms](https://jwt.ms).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Applicazioni** e quindi **Aggiungi**.
5. Immettere un nome per l'applicazione, ad esempio *apptest1*.
6. Per **App Web/API Web** selezionare `Yes` e quindi immettere `https://jwt.ms` in **URL di risposta**.
7. Fare clic su **Create**(Crea).

### <a name="create-the-user-flow"></a>Creare il flusso utente

Per testare i file di personalizzazione, occorre creare un flusso utente predefinito di iscrizione o accesso che usa l'applicazione creata in precedenza.

1. Dal tenant di Azure AD B2C, selezionare **Flussi utente** e fare clic su **Nuovo flusso utente**.
2. Nella scheda **Consigliati** fare clic su **Iscrizione e accesso**.
3. Immettere un nome per il flusso utente. Ad esempio, *signup_signin*. Il prefisso *B2C_1* viene aggiunto automaticamente al nome al momento della creazione del flusso utente.
4. In **Provider di identità** selezionare **Iscrizione posta elettronica**.
5. In **Attributi e attestazioni utente** fare clic su **Mostra dettagli**.
6. Nella colonna **Raccogli l'attributo** scegliere gli attributi da raccogliere dal cliente durante l'iscrizione. Impostare ad esempio **Paese/Area geografica**, **Nome visualizzato** e **Codice postale**.
7. Nella colonna **Restituisci l'attestazione** scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione al completamento dell'esperienza di iscrizione o accesso. Selezionare ad esempio **Nome visualizzato**, **Provider di identità**, **Codice postale**, **Nuovo utente** e **ID oggetto dell'utente**.
8. Fare clic su **OK**.
9. Fare clic su **Create**(Crea).
10. In **Personalizza**, selezionare **Layout di pagina**. Selezionare **Pagina unificata per l'iscrizione o l'accesso**, quindi fare clic su **Sì** per **Usa il contenuto della pagina personalizzata**.
11. In **URI della pagina personalizzata** immettere l'URL del file *custom-ui.html* registrato in precedenza.
12. Nella parte superiore della pagina fare clic su **Salva**.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Dal tenant di Azure AD B2C, selezionare **Flussi utente** e selezionare il flusso utente creato. Ad esempio, *B2C_1_signup_signin*.
2. Nella parte superiore della pagina, fare clic su **Esegui il flusso utente**.
3. Fare clic sul pulsante **Esegui il flusso utente**.

    ![Eseguire il flusso utente di iscrizione o accesso](./media/tutorial-customize-ui/run-user-flow.png)

    Dovrebbe essere visualizzata una pagina simile all'esempio seguente, con gli elementi allineati al centro in base al file CSS creato:

    ![Risultati del flusso utente](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare file di personalizzazione dell'interfaccia utente
> * Creare un flusso utente di iscrizione e di accesso che usa i file
> * Testare l'interfaccia utente personalizzata

> [!div class="nextstepaction"]
> [Personalizzazione della lingua in Azure AD B2C](active-directory-b2c-reference-language-customization.md)