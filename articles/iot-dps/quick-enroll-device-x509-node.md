---
title: Registrare dispositivi X.509 nel servizio Device Provisioning di Azure con Node.js
description: Questa guida introduttiva usa registrazioni di gruppo. In questa guida di avvio rapido verrà eseguita la registrazione dei dispositivi X.509 nel servizio Device Provisioning in hub IoT di Azure con l'SDK per servizi Node.js.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 68f274fb50b883c6f252a78f97f31e49e72b135c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974708"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-nodejs"></a>Guida introduttiva: Registrare i dispositivi X.509 nel servizio Device Provisioning con Node.js

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Questa guida introduttiva illustra come usare Node.js per creare un [gruppo di registrazione](concepts-service.md#enrollment-group) a livello di codice che usa un certificato X.509 intermedio o CA radice. Il gruppo di registrazione viene creato usando [IoT SDK per Node.js](https://github.com/Azure/azure-iot-sdk-node) e un'applicazione Node.js di esempio. Un gruppo di registrazione controlla l'accesso al servizio di provisioning per i dispositivi che condividono un certificato di firma comune nella rispettiva catena di certificati. Per altre informazioni, vedere [Controllo dell'accesso dei dispositivi al servizio di provisioning con certificati X.509](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Per altre informazioni sull'uso di un'infrastruttura a chiave pubblica (PKI, Public Key Infrastructure) basata su certificati X.509 con l'hub IoT di Azure e il servizio Device Provisioning, vedere [Panoramica della sicurezza dei certificati della CA X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Questa guida introduttiva prevede che siano già stati creati un hub IoT e un'istanza del servizio Device Provisioning. Se queste risorse non sono state ancora create, completare la guida introduttiva [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md) prima di continuare con questo articolo.

Anche se i passaggi illustrati in questo articolo funzionano su computer sia Windows che Linux, l'articolo usa un computer di sviluppo Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisiti

- Installare [Node.js v4.0 o versione successiva](https://nodejs.org).
- Installare [Git](https://git-scm.com/download/).


## <a name="prepare-test-certificates"></a>Preparare i certificati di test

Per questa guida introduttiva è necessario un file PEM o CER contenente la parte pubblica di un certificato X.509 intermedio o CA radice. Questo certificato deve essere caricato nel servizio di provisioning e verificato dal servizio. 

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) contiene strumenti di test che consentono di creare una catena di certificati X.509, caricare un certificato intermedio o radice dalla catena ed eseguire la proof-of-possession con il servizio per verificare il certificato. I certificati creati con gli strumenti dell'SDK sono progettati per essere usati solo per **test di sviluppo**. Questi certificati **non devono essere usati nell'ambiente di produzione**. Contengono password hardcoded ("1234") che scadono dopo 30 giorni. Per informazioni su come ottenere certificati idonei all'uso nell'ambiente di produzione, vedere [Come ottenere un certificato della CA X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) nella documentazione dell'hub IoT di Azure.

Per usare gli strumenti di test per generare certificati, seguire questa procedura: 
 
1. Aprire un prompt dei comandi o la shell Git Bash e passare a una cartella di lavoro nel computer in uso. Eseguire il comando seguente per clonare il repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   Il completamento di questa operazione richiederà alcuni minuti.

   Gli strumenti di test si trovano nel percorso *azure-iot-sdk-c/tools/CACertificates* del repository clonato.    

2. Seguire la procedura in [Gestione dei certificati CA di prova per esempi e certificazioni](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). 



## <a name="create-the-enrollment-group-sample"></a>Creare l'esempio di gruppo di registrazioni 

 
1. In una finestra di comando della cartella di lavoro eseguire:
  
     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. Con un editor di testo creare un file **create_enrollment_group.js** nella cartella di lavoro. Aggiungere il codice seguente al file e salvare:

    ```
    'use strict';
    var fs = require('fs');

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);

    var enrollment = {
      enrollmentGroupId: 'first',
      attestation: {
        type: 'x509',
        x509: {
          signingCertificates: {
            primary: {
              certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
            }
          }
        }
      },
      provisioningStatus: 'disabled'
    };

    serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the group enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
        enrollmentResponse.provisioningStatus = 'enabled';
        serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
          if (err) {
            console.log('error updating the group enrollment: ' + err);
          } else {
            console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
          }
        });
      }
    });
    ```

## <a name="run-the-enrollment-group-sample"></a>Eseguire l'esempio di gruppo di registrazioni
 
1. Per eseguire l'esempio è necessaria la stringa di connessione del servizio di provisioning. 
    1. Accedere al portale di Azure, selezionare il pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio Device Provisioning. 
    2. Fare clic su **Criteri di accesso condiviso** e quindi selezionare i criteri di accesso da usare per aprirne le proprietà. Nella finestra **Criteri di accesso** copiare e annotare la stringa di connessione della chiave primaria. 

       ![Ottenere la stringa di connessione del servizio di provisioning dal portale](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. Come indicato in [Preparare i certificati di test](quick-enroll-device-x509-node.md#prepare-test-certificates), è anche necessario un file PEM contenente un certificato X.509 intermedio o CA radice caricato e verificato in precedenza con il servizio di provisioning. Per verificare che il certificato sia stato caricato e verificato, nella pagina di riepilogo del servizio Device Provisioning del portale di Azure selezionare **Certificati**. Trovare il certificato che si vuole usare per la registrazione del gruppo e verificare che il valore di stato sia *verificato*.

    ![Certificato verificato nel portale](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. Per creare un gruppo di registrazioni per il certificato eseguire questo comando, includendo le virgolette che racchiudono gli argomenti:
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. Al termine della creazione, la finestra di comando visualizza le proprietà del nuovo gruppo di registrazioni.

    ![Proprietà delle registrazioni nell'output del comando](./media/quick-enroll-device-x509-node/sample-output.png) 

4. Verificare che il gruppo di registrazioni sia stato creato. Nel portale di Azure, nel pannello di riepilogo del servizio Device Provisioning selezionare **Gestisci registrazioni**. Selezionare la scheda **Gruppi di registrazioni** e verificare che la nuova voce di registrazione (la *prima*) sia presente.

    ![Proprietà delle registrazioni nel portale](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>Pulire le risorse
Se si prevede di esplorare gli esempi di servizio Node.js, non eseguire la pulizia delle risorse create in questa guida di avvio rapido. Se non si intende continuare, seguire questa procedura per eliminare tutte le risorse di Azure create in questa guida.
 
1. Chiudere la finestra di output di esempio di Node.js nel computer.
2. Passare al servizio Device Provisioning nel portale di Azure, selezionare **Gestisci registrazioni** e quindi la scheda **Gruppi di registrazioni**. Selezionare la casella di controllo accanto a *NOME GRUPPO* per i dispositivi X.509 registrati con questa guida di avvio rapido e fare clic sul pulsante **Elimina** nella parte superiore del riquadro.    
3. Nel servizio Device Provisioning nel portale di Azure selezionare **Certificati** e quindi il certificato caricato per questa guida di avvio rapido e fare clic sul pulsante **Elimina** nella parte superiore della finestra **Dettagli certificato**.  
 
## <a name="next-steps"></a>Passaggi successivi
In questa guida di avvio rapido è stata creata una registrazione di gruppo per un certificato X.509 CA radice o intermedio usando il servizio Device Provisioning in hub IoT di Azure. Per informazioni approfondite sul provisioning del dispositivo, passare all'esercitazione sulla configurazione del servizio Device Provisioning nel portale di Azure. 
 
> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials (Esercitazioni sul servizio Device Provisioning in hub IoT di Azure)](./tutorial-set-up-cloud.md)