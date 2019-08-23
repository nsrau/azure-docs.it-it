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
ms.openlocfilehash: a819e0b5a0da92137451eedbf84fe06d22879568
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973779"
---
# <a name="how-to-use-the-opc-ua-certificate-management-service"></a>Come usare il servizio di gestione dei certificati OPC UA

Questo articolo illustra come registrare le applicazioni e come rilasciare i certificati dell'applicazione firmata per i dispositivi OPC UA.

## <a name="prerequisites"></a>Prerequisiti

### <a name="deploy-the-certificate-management-service"></a>Distribuire il servizio di gestione certificati

Prima di tutto, il servizio deve essere distribuito nel cloud di Azure.
Trovare un articolo che descrive come [distribuire il servizio di gestione certificati dell'](howto-opc-vault-deploy.md)insieme di credenziali OPC.

### <a name="create-the-issuer-ca-certificate"></a>Creare il certificato CA dell'autorità emittente

Se non è ancora stato fatto, creare il certificato CA dell'autorità emittente.

Trovare un articolo che descrive come [creare e gestire il certificato dell'autorità emittente per l'insieme di credenziali OPC](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Applicazioni OPC UA sicure

### <a name="step-1-register-your-opc-ua-application"></a>Passaggio 1: Registrare l'applicazione OPC UA 

> [!IMPORTANT]
> Il ruolo ' Writer ' è necessario per la registrazione di un'applicazione.

1. Aprire il servizio certificati all' `https://myResourceGroup-app.azurewebsites.net` indirizzo ed accedere.
2. Passare alla pagina `Register New`.
1. Per la registrazione di un'applicazione, è necessario che l'utente disponga almeno del ruolo ' Writer ' assegnato.
2. Il modulo di immissione segue le convenzioni di denominazione nel mondo OPC UA. Ad esempio, nell'immagine seguente le impostazioni per l'esempio [OPC UA Reference Server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) in OPC UA. Viene visualizzato lo stack NetStandard:

   ![Registrazione server di riferimento UA](media/howto-opc-vault-secure/reference-server-registration.png "Registrazione server di riferimento UA")

5. Premere il `Register` pulsante per registrare l'applicazione nel database dell'applicazione di servizio certificati. Il flusso di lavoro Guida direttamente l'utente al passaggio successivo per richiedere un certificato firmato per l'applicazione.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Passaggio 2: Proteggere l'applicazione con un certificato dell'applicazione firmata dall'autorità di certificazione

È possibile proteggere un'applicazione OPC UA emettendo un certificato firmato basato su una richiesta di firma del certificato (CSR) o richiedendo una nuova coppia di chiavi, che include anche una nuova chiave privata nel formato PFX o PEM. Seguire la documentazione del dispositivo OPC UA in cui è supportato il metodo per l'applicazione. In generale, è consigliabile usare il metodo CSR, perché non richiede il trasferimento di una chiave privata in rete.

- Se il dispositivo richiede l'emissione di una nuova coppia di chiavi, seguire [Step3a](##step-3a-request-a-new-certificate-with-a-new-keypair).
- Se il dispositivo supporta per emettere una richiesta di firma del certificato (CSR), seguire [Step3b](#step-3b-request-a-new-certificate-with-a-csr).

### <a name="step-3a-request-a-new-certificate-with-a-new-keypair"></a>Passaggio 3a: Richiedere un nuovo certificato con una nuova coppia di coppie

1. Passare alla pagina `Applications`.
3. Avviare il flusso di lavoro della richiesta `New Request` di certificato scegliendo per un'applicazione elencata.

   ![Richiedi nuovo certificato](media/howto-opc-vault-secure/request-new-certificate.png "Richiedi nuovo certificato")

3. Premere ' Richiedi nuova coppia di chiavi e certificato ' per richiedere una chiave privata e un nuovo certificato firmato con la chiave pubblica per l'applicazione.

   ![Genera nuova coppia di chiavi](media/howto-opc-vault-secure/generate-new-key-pair.png "Genera nuova coppia di chiavi")

4. Compilare il modulo con un oggetto, i nomi di dominio e scegliere PEM o PFX con password per la chiave privata. Premere il `Generate New Certificate` pulsante per creare la richiesta di certificato.

   ![Approva certificato](media/howto-opc-vault-secure/approve-reject.png "Approva certificato")

5. Il passaggio di approvazione richiede un utente con ruolo "responsabile approvazione" e con autorizzazioni di firma in Azure Key Vault. Nel flusso di lavoro tipico, il responsabile approvazione e il ruolo richiedente devono essere assegnati a utenti diversi.
6. Approva o rifiuta la richiesta di certificato per avviare o annullare la creazione effettiva della coppia di chiavi e dell'operazione di firma. La nuova coppia di chiavi viene creata e archiviata in modo sicuro in Azure Key Vault finché non viene scaricata dal richiedente del certificato. Il certificato risultante con chiave pubblica è firmato dalla CA. Il completamento di queste operazioni può richiedere alcuni secondi.

   ![Visualizzazione della coppia di chiavi](media/howto-opc-vault-secure/view-key-pair.png "Visualizzazione della coppia di chiavi")

7. La chiave privata risultante (PFX o PEM) e il certificato (DER) possono essere scaricati da qui nel formato selezionato come download di file binario. È disponibile anche una versione con codifica Base64, ad esempio, per copiare incollare il certificato in una riga di comando o in una voce di testo. 
8. Una volta che la chiave privata viene scaricata e archiviata in modo sicuro, è possibile eliminarla `Delete Private Key` dal servizio con il pulsante. Il certificato con la chiave pubblica rimane disponibile per un uso futuro.
9. A causa dell'utilizzo di un certificato firmato da un'autorità di certificazione, è necessario scaricare anche il certificato CA e il CRL.
10. A questo punto, dipende dal dispositivo OPC UA come applicare la nuova coppia di chiavi. In genere, il certificato CA e il CRL vengono copiati in una `trusted` cartella, mentre la chiave pubblica e privata del certificato dell'applicazione viene applicata a una `own` cartella nell'archivio certificati. Alcuni dispositivi potrebbero già supportare "push server" per gli aggiornamenti del certificato. Vedere la documentazione del dispositivo OPC UA.

### <a name="step-3b-request-a-new-certificate-with-a-csr"></a>Passaggio 3b: Richiedere un nuovo certificato con CSR 

1. Passare alla pagina `Applications`.
3. Avviare il flusso di lavoro della richiesta `New Request` di certificato scegliendo per un'applicazione elencata.

   ![Richiedi nuovo certificato](media/howto-opc-vault-secure/request-new-certificate.png "Richiedi nuovo certificato")

3. Premere "Richiedi nuovo certificato con richiesta di firma" per richiedere un nuovo certificato firmato per l'applicazione.

   ![Genera nuovo certificato](media/howto-opc-vault-secure/generate-new-certificate.png "Genera nuovo certificato")

4. Caricare CSR selezionando un file locale o incollando un CSR con codifica Base64 nel formato. Premere il `Generate New Certificate` pulsante per creare la richiesta di certificato.

   ![Approva CSR](media/howto-opc-vault-secure/approve-reject-csr.png "Approva CSR")

5. Il passaggio di approvazione richiede un utente con ruolo "responsabile approvazione" e con autorizzazioni di firma in Azure Key Vault. Approva o rifiuta la richiesta di certificato per avviare o annullare l'operazione di firma effettiva. Il certificato risultante con chiave pubblica è firmato dalla CA. Il completamento di questa operazione può richiedere alcuni secondi.

   ![Visualizza certificato](media/howto-opc-vault-secure/view-cert-csr.png "Visualizza certificato")

6. Il certificato risultante (DER) può essere scaricato da qui come file binario. È disponibile anche una versione con codifica Base64, ad esempio, per copiare incollare il certificato in una riga di comando o in una voce di testo. 
10. Una volta che il certificato è stato scaricato e archiviato in modo sicuro, è possibile eliminarlo dal `Delete Certificate` servizio con il pulsante.
11. A causa dell'utilizzo di un certificato firmato da un'autorità di certificazione, è necessario scaricare anche il certificato CA e il CRL.
12. A questo punto, dipende dal dispositivo OPC UA come applicare il nuovo certificato. In genere, il certificato CA e il CRL vengono copiati in una `trusted` cartella, mentre il certificato dell'applicazione viene applicato a una `own` cartella nell'archivio certificati. Alcuni dispositivi potrebbero già supportare "push server" per gli aggiornamenti del certificato. Vedere la documentazione del dispositivo OPC UA.

### <a name="step-4-device-secured"></a>Passaggio 4: Dispositivo protetto

Il dispositivo OPC UA è ora pronto per comunicare con altri dispositivi OPC UA protetti da certificati firmati dall'autorità di certificazione senza ulteriori configurazioni. Buona lettura.


## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come proteggere i dispositivi OPC UA, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Eseguire un servizio di gestione certificati sicura](howto-opc-vault-secure-ca.md)