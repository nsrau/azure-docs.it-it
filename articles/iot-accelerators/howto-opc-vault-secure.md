---
title: Proteggere la comunicazione dei dispositivi OPC UA con l'insieme di credenziali OPC-Azure | Microsoft Docs
description: Come registrare le applicazioni OPC UA e come rilasciare i certificati dell'applicazione firmata per i dispositivi OPC UA con l'insieme di credenziali OPC.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b5c886625c944e2f5501859e78506ca89ec3d765
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71203684"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Usare il servizio di gestione certificati dell'insieme di credenziali OPC

Questo articolo illustra come registrare le applicazioni e come rilasciare i certificati dell'applicazione firmata per i dispositivi OPC UA.

## <a name="prerequisites"></a>Prerequisiti

### <a name="deploy-the-certificate-management-service"></a>Distribuire il servizio di gestione certificati

Prima di tutto, distribuire il servizio nel cloud di Azure. Per informazioni dettagliate, vedere [deploy the OPC Vault Certificate Management Service](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Creare il certificato CA dell'autorità emittente

Se non è ancora stato fatto, creare il certificato CA dell'autorità emittente. Per informazioni dettagliate, vedere [creare e gestire il certificato dell'autorità emittente per l'insieme di credenziali OPC](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Applicazioni OPC UA sicure

### <a name="step-1-register-your-opc-ua-application"></a>Passaggio 1: registrare l'applicazione OPC UA 

> [!IMPORTANT]
> Il ruolo writer è necessario per registrare un'applicazione.

1. Aprire il servizio certificati all'`https://myResourceGroup-app.azurewebsites.net` ed accedere.
2. Passare a **Registra nuovo**. Per la registrazione di un'applicazione, un utente deve avere almeno il ruolo di writer assegnato.
2. Il modulo di immissione segue le convenzioni di denominazione in OPC UA. Nello screenshot seguente, ad esempio, vengono visualizzate le impostazioni per l'esempio [OPC UA Reference Server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) nello stack opc UA .NET standard:

   ![Screenshot della registrazione del server di riferimento UA](media/howto-opc-vault-secure/reference-server-registration.png "Registrazione server di riferimento UA")

5. Selezionare **Register (registra** ) per registrare l'applicazione nel database dell'applicazione di servizio certificati. Il flusso di lavoro Guida direttamente l'utente al passaggio successivo per richiedere un certificato firmato per l'applicazione.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Passaggio 2: proteggere l'applicazione con un certificato dell'applicazione firmata dall'autorità di certificazione

Proteggere l'applicazione OPC UA emettendo un certificato firmato in base a una richiesta di firma del certificato (CSR). In alternativa, è possibile richiedere una nuova coppia di chiavi, che include una nuova chiave privata in formato PFX o PEM. Per informazioni sul metodo supportato per l'applicazione, vedere la documentazione del dispositivo OPC UA. In generale, è consigliabile usare il metodo CSR, perché non richiede il trasferimento di una chiave privata in rete.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Richiedere un nuovo certificato con una nuova coppia di coppie

1. Passare ad **applicazioni**.
3. Selezionare **nuova richiesta** per un'applicazione elencata.

   ![Screenshot del nuovo certificato di richiesta](media/howto-opc-vault-secure/request-new-certificate.png "Richiedi nuovo certificato")

3. Selezionare **Richiedi nuova coppia di chiavi e certificato** per richiedere una chiave privata e un nuovo certificato firmato con la chiave pubblica per l'applicazione.

   ![Screenshot di genera una nuova coppia di coppie di coppie e certificati](media/howto-opc-vault-secure/generate-new-key-pair.png "Genera nuova coppia di chiavi")

4. Compilare il modulo con un oggetto e i nomi di dominio. Per la chiave privata, scegliere PEM o PFX con password. Selezionare **genera una nuova coppia di portacoppie** per creare la richiesta di certificato.

   ![Screenshot della visualizzazione dei dettagli della richiesta di certificato](media/howto-opc-vault-secure/approve-reject.png "Approva certificato")

5. L'approvazione richiede un utente con il ruolo di responsabile approvazione e con autorizzazioni di firma in Azure Key Vault. Nel flusso di lavoro tipico, i ruoli del responsabile approvazione e del richiedente devono essere assegnati a utenti diversi. Selezionare **approva** o **rifiuta** per avviare o annullare la creazione effettiva della coppia di chiavi e dell'operazione di firma. La nuova coppia di chiavi viene creata e archiviata in modo sicuro in Azure Key Vault, fino a quando non viene scaricata dal richiedente del certificato. Il certificato risultante con chiave pubblica è firmato dalla CA. Il completamento di queste operazioni può richiedere alcuni secondi.

   ![Screenshot della visualizzazione dei dettagli della richiesta di certificato, con messaggio di approvazione in basso](media/howto-opc-vault-secure/view-key-pair.png "Visualizzazione della coppia di chiavi")

7. La chiave privata risultante (PFX o PEM) e il certificato (DER) possono essere scaricati da qui nel formato selezionato come download di file binario. È disponibile anche una versione con codifica Base64, ad esempio, per copiare e incollare il certificato in una riga di comando o in una voce di testo. 
8. Dopo aver scaricato e archiviato la chiave privata in modo sicuro, è possibile selezionare **Elimina chiave privata**. Il certificato con la chiave pubblica rimane disponibile per un uso futuro.
9. A causa dell'utilizzo di un certificato firmato da un'autorità di certificazione, è necessario scaricare anche il certificato CA e l'elenco di revoche di certificati (CRL).

A questo punto, dipende dal dispositivo OPC UA come applicare la nuova coppia di chiavi. In genere, il certificato CA e il CRL vengono copiati in una cartella `trusted`, mentre le chiavi pubbliche e private del certificato dell'applicazione vengono applicate a una cartella di `own` nell'archivio certificati. Alcuni dispositivi potrebbero già supportare il push del server per gli aggiornamenti del certificato. Vedere la documentazione del dispositivo OPC UA.

#### <a name="request-a-new-certificate-with-a-csr"></a>Richiedere un nuovo certificato con CSR 

1. Passare ad **applicazioni**.
3. Selezionare **nuova richiesta** per un'applicazione elencata.

   ![Screenshot del nuovo certificato di richiesta](media/howto-opc-vault-secure/request-new-certificate.png "Richiedi nuovo certificato")

3. Selezionare **Richiedi nuovo certificato con richiesta di firma** per richiedere un nuovo certificato firmato per l'applicazione.

   ![Screenshot della generazione di un nuovo certificato](media/howto-opc-vault-secure/generate-new-certificate.png "Genera nuovo certificato")

4. Caricare CSR selezionando un file locale o incollando un CSR con codifica Base64 nel formato. Selezionare **genera nuovo certificato**.

   ![Screenshot della visualizzazione dei dettagli della richiesta di certificato](media/howto-opc-vault-secure/approve-reject-csr.png "Approva CSR")

5. L'approvazione richiede un utente con il ruolo di responsabile approvazione e con autorizzazioni di firma in Azure Key Vault. Selezionare **approva** o **rifiuta** per avviare o annullare l'operazione di firma effettiva. Il certificato risultante con chiave pubblica è firmato dalla CA. Il completamento di questa operazione può richiedere alcuni secondi.

   ![Screenshot della visualizzazione dei dettagli della richiesta di certificato, con messaggio di approvazione in basso](media/howto-opc-vault-secure/view-cert-csr.png "Visualizza certificato")

6. Il certificato risultante (DER) può essere scaricato da qui come file binario. È disponibile anche una versione con codifica Base64, ad esempio, per copiare e incollare il certificato in una riga di comando o in una voce di testo. 
10. Dopo che il certificato è stato scaricato e archiviato in modo sicuro, è possibile selezionare **Elimina certificato**.
11. A causa dell'utilizzo di un certificato firmato da un'autorità di certificazione, è necessario scaricare anche il certificato CA e il CRL.

A questo punto, dipende dal dispositivo OPC UA come applicare il nuovo certificato. In genere, il certificato CA e il CRL vengono copiati in una cartella `trusted`, mentre il certificato dell'applicazione viene applicato a una cartella di `own` nell'archivio certificati. Alcuni dispositivi potrebbero già supportare il push del server per gli aggiornamenti del certificato. Vedere la documentazione del dispositivo OPC UA.

### <a name="step-4-device-secured"></a>Passaggio 4: dispositivo protetto

Il dispositivo OPC UA è ora pronto per comunicare con altri dispositivi OPC UA protetti da certificati della CA firmati, senza ulteriori configurazioni.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come proteggere i dispositivi OPC UA, è possibile:

> [!div class="nextstepaction"]
> [Eseguire un servizio di gestione certificati sicura](howto-opc-vault-secure-ca.md)