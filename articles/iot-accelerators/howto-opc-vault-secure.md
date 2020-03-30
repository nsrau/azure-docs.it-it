---
title: Proteggere la comunicazione dei dispositivi OPC UA con OPC Vault - Azure Documenti Microsoft
description: Come registrare le applicazioni OPC UA e come emettere certificati di applicazioni firmati per i dispositivi OPC UA con OPC Vault.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: af5e511cbf273bc4e4fa0a08d089a955426fe75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75454201"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Utilizzare il servizio di gestione dei certificati OPC Vault

Questo articolo spiega come registrare le applicazioni e come emettere certificati di applicazioni firmati per i dispositivi OPC UA.

## <a name="prerequisites"></a>Prerequisiti

### <a name="deploy-the-certificate-management-service"></a>Distribuire il servizio di gestione dei certificatiDeploy the certificate management service

Distribuire innanzitutto il servizio nel cloud di Azure.First, deploy the service to the Azure cloud. Per informazioni dettagliate, vedere Distribuire il servizio di [gestione dei certificati OPC Vault](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Creare il certificato CA dell'autorità di certificazione dell'autorità emittente

Se non è stato ancora fatto, creare il certificato CA dell'autorità emittente. Per informazioni dettagliate, consultate [Creare e gestire il certificato dell'autorità emittente per OPC Vault.](howto-opc-vault-manage.md)

## <a name="secure-opc-ua-applications"></a>Applicazioni OPC UA sicure

### <a name="step-1-register-your-opc-ua-application"></a>Passaggio 1: Registra l'applicazione OPC UA 

> [!IMPORTANT]
> Il ruolo Writer è necessario per registrare un'applicazione.

1. Aprire il servizio `https://myResourceGroup-app.azurewebsites.net`certificati all'indirizzo e accedere.
2. Vai a **Registra nuovo**. Per la registrazione di un'applicazione, un utente deve disporre almeno del ruolo Writer assegnato.
2. Il modulo di immissione segue le convenzioni di denominazione in OPC UA. Ad esempio, nella schermata seguente, vengono visualizzate le impostazioni per l'esempio [OPC UA Reference Server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) nello stack OPC UA .NET Standard:

   ![Schermata della registrazione del server di riferimento UA](media/howto-opc-vault-secure/reference-server-registration.png "Registrazione server di riferimento UAUA Reference Server Registration")

5. Selezionare **Registra** per registrare l'applicazione nel database dell'applicazione di servizio certificati. Il flusso di lavoro guida direttamente l'utente al passaggio successivo per richiedere un certificato firmato per l'applicazione.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Passaggio 2: Proteggere l'applicazione con un certificato dell'applicazione firmata da un'autorità di certificazioneStep 2: Secure your application with a CA signed application certificate

Proteggere l'applicazione OPC UA emettendo un certificato firmato basato su una richiesta di firma del certificato (CSR). In alternativa, è possibile richiedere una nuova coppia di chiavi, che include una nuova chiave privata in formato PFX o PEM. Per informazioni sul metodo supportato per l'applicazione, vedere la documentazione del dispositivo OPC UA. In generale, il metodo CSR è consigliato, perché non richiede una chiave privata da trasferire su un filo.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Richiedere un nuovo certificato con una nuova coppia di chiavi

1. Vai ad **Applicazioni**.
3. Selezionare **Nuova richiesta** per un'applicazione elencata.

   ![Screenshot di Richiedi nuovo certificato](media/howto-opc-vault-secure/request-new-certificate.png "Richiedi nuovo certificato")

3. Selezionare **Richiedi nuova KeyPair e Certificato** per richiedere una chiave privata e un nuovo certificato firmato con la chiave pubblica per l'applicazione.

   ![Screenshot di Generate a New KeyPair e Certificate](media/howto-opc-vault-secure/generate-new-key-pair.png "Genera nuova coppia di chiavi")

4. Compila il modulo con un oggetto e i nomi di dominio. Per la chiave privata, scegliere PEM o PFX con password. Selezionare **Genera nuova keyPair** per creare la richiesta di certificato.

   ![Screenshot di Visualizza dettagli richiesta certificato](media/howto-opc-vault-secure/approve-reject.png "Approva certificato")

5. L'approvazione richiede un utente con il ruolo Approvatore e con autorizzazioni di firma nell'insieme di credenziali delle chiavi di Azure.Approval requires a user with the Approver role, and with signing permissions in Azure Key Vault. Nel flusso di lavoro tipico, i ruoli Approvatore e Richiedente devono essere assegnati a utenti diversi. Selezionare **Approva** o **Rifiuta** per avviare o annullare la creazione effettiva della coppia di chiavi e l'operazione di firma. La nuova coppia di chiavi viene creata e archiviata in modo sicuro nell'insieme di credenziali delle chiavi di Azure, fino a quando non viene scaricata dal richiedente del certificato. Il certificato risultante con chiave pubblica viene firmato dalla CA. Il completamento di queste operazioni può richiedere alcuni secondi.

   ![Screenshot di Visualizza dettagli richiesta di certificato, con il messaggio di approvazione in basso](media/howto-opc-vault-secure/view-key-pair.png "Visualizza coppia di chiavi")

7. La chiave privata risultante (PFX o PEM) e il certificato (DER) possono essere scaricati da qui nel formato selezionato come download di file binari. È disponibile anche una versione con codifica base64, ad esempio per copiare e incollare il certificato in una riga di comando o in una voce di testo. 
8. Dopo aver scaricato e archiviato la chiave privata in modo sicuro, è possibile selezionare **Elimina chiave privata**. Il certificato con la chiave pubblica rimane disponibile per un utilizzo futuro.
9. A causa dell'utilizzo di un certificato CA firmato, il certificato CA e l'elenco di revoche di certificati (CRL) devono essere scaricati anche qui.

Ora dipende dal dispositivo OPC UA come applicare la nuova coppia di chiavi. In genere, il certificato CA e `trusted` il CRL vengono copiati in una cartella, mentre le chiavi pubbliche e private del certificato dell'applicazione vengono applicate a una `own` cartella nell'archivio certificati. Alcuni dispositivi potrebbero già supportare il push del server per gli aggiornamenti dei certificati. Fare riferimento alla documentazione del dispositivo OPC UA.

#### <a name="request-a-new-certificate-with-a-csr"></a>Richiedere un nuovo certificato con una CSR 

1. Vai ad **Applicazioni**.
3. Selezionare **Nuova richiesta** per un'applicazione elencata.

   ![Screenshot di Richiedi nuovo certificato](media/howto-opc-vault-secure/request-new-certificate.png "Richiedi nuovo certificato")

3. Selezionare **Richiedi nuovo certificato con richiesta** di firma per richiedere un nuovo certificato firmato per l'applicazione.

   ![Screenshot di Generare un nuovo certificato](media/howto-opc-vault-secure/generate-new-certificate.png "Genera nuovo certificato")

4. Caricare CSR selezionando un file locale o incollando una CSR con codifica base64 nel modulo. Selezionare **Genera nuovo certificato**.

   ![Screenshot di Visualizza dettagli richiesta certificato](media/howto-opc-vault-secure/approve-reject-csr.png "Approva CSR")

5. L'approvazione richiede un utente con il ruolo Approvatore e con autorizzazioni di firma nell'insieme di credenziali delle chiavi di Azure.Approval requires a user with the Approver role, and with signing permissions in Azure Key Vault. Selezionare **Approva** o **Rifiuta** per avviare o annullare l'operazione di firma effettiva. Il certificato risultante con chiave pubblica viene firmato dalla CA. Il completamento dell'operazione può richiedere alcuni secondi.

   ![Screenshot di Visualizza dettagli richiesta di certificato, con il messaggio di approvazione in basso](media/howto-opc-vault-secure/view-cert-csr.png "View Certificate")

6. Il certificato risultante (DER) può essere scaricato da qui come file binario. È disponibile anche una versione con codifica base64, ad esempio per copiare e incollare il certificato in una riga di comando o in una voce di testo. 
10. Dopo aver scaricato e archiviato il certificato in modo sicuro, è possibile selezionare **Elimina certificato**.
11. A causa dell'utilizzo di un certificato CA firmato, il certificato CA e il CRL devono essere scaricati anche qui.

Ora dipende dal dispositivo OPC UA come applicare il nuovo certificato. In genere, il certificato CA e `trusted` il CRL vengono copiati `own` in una cartella, mentre il certificato dell'applicazione viene applicato a una cartella nell'archivio certificati. Alcuni dispositivi potrebbero già supportare il push del server per gli aggiornamenti dei certificati. Fare riferimento alla documentazione del dispositivo OPC UA.

### <a name="step-3-device-secured"></a>Passaggio 3: Dispositivo protetto

Il dispositivo OPC UA è ora pronto per comunicare con altri dispositivi OPC UA protetti da certificati firmati da CA, senza ulteriori configurazioni.

## <a name="next-steps"></a>Passaggi successivi

Ora che hai imparato a proteggere i dispositivi OPC UA, puoi:

> [!div class="nextstepaction"]
> [Eseguire un servizio di gestione dei certificati protettoRun a secure certificate management service](howto-opc-vault-secure-ca.md)
