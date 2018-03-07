---
title: Come eseguire una verifica del possesso per certificati della CA X.509 con il servizio Device Provisioning in hub IoT | Microsoft Docs
description: Come verificare certificati della CA X.509 con il servizio Device Provisioning
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 02/26/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 07fe5f975e59c10fcd716db6585e2ae0fefc90e4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Come eseguire una verifica del possesso per certificati della CA X.509 con il servizio Device Provisioning

Un certificato della CA X.509 è un certificato della CA caricato e registrato nel servizio di provisioning e sottoposto alla verifica del possesso con il servizio stesso. 

La verifica del possesso prevede i passaggi seguenti:
1. Ottenere un codice di verifica univoco generato dal servizio di provisioning per il certificato della CA X.509. Questa operazione può essere eseguita dal portale di Azure.
2. Creare un certificato di verifica X.509 con il codice di verifica come oggetto e firmare il certificato con la chiave privata associata al certificato della CA X.509 stesso.
3. Caricare il certificato di verifica firmato nel servizio. Il servizio convalida il certificato di verifica usando la parte pubblica del certificato della CA da verificare, dimostrando quindi che l'utente è in possesso della chiave privata del certificato.

I certificati verificati svolgono un ruolo importante nell'uso dei gruppi di registrazione. La verifica della proprietà dei certificati costituisce un livello di sicurezza aggiuntivo che garantisce che l'autore del caricamento del certificato è in possesso della chiave privata del certificato stesso. La verifica impedisce a un attore malintenzionato di effettuare l'hijack dei dispositivi intercettando il traffico tramite l'estrazione di un certificato intermedio e usando tale certificato per creare un gruppo di registrazione nel proprio servizio di provisioning. Dimostrando la proprietà della radice o di un certificato intermedio in una catena di certificati, si dimostra di avere l'autorizzazione a generare certificati foglia per i dispositivi che verranno registrati come appartenenti a tale gruppo di registrazione. Per questo motivo, la radice o il certificato intermedio configurato in un gruppo di registrazione deve essere un certificato verificato oppure deve fare riferimento a un certificato verificato nella catena di certificati presentata da un dispositivo quando esegue l'autenticazione con il servizio. Per altre informazioni sui gruppi di registrazione, vedere [Certificati X.509](concepts-security.md#x509-certificates) e [Controllo dell'accesso dei dispositivi al servizio di provisioning con certificati X.509](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registrare la parte pubblica di un certificato X.509 e ottenere un codice di verifica

Per registrare un certificato della CA con il servizio di provisioning e ottenere un codice di verifica utilizzabile durante la verifica del possesso, seguire questa procedura. 

1. Nel portale di Azure passare al servizio di provisioning e aprire **Certificati** dal menu a sinistra. 
2. Fare clic su **Aggiungi** per aggiungere un nuovo certificato.
3. Immettere un nome visualizzato descrittivo per il certificato. Individuare il file con estensione cer o pem che rappresenta la parte pubblica del certificato X.509. Fare clic su **Carica**.
4. Quando si riceve una notifica che il certificato è stato caricato correttamente, fare clic su **Salva**.

    ![Caricamento del certificato](./media/how-to-verify-certificates/add-new-cert.png)  

   Il certificato verrà visualizzato nell'elenco **Esplora certificati**. Si noti che lo **STATO** di questo certificato è *Non verificato*.

5. Fare clic sul certificato aggiunto nel passaggio precedente.

6. In **Dettagli certificato** fare clic su **Genera codice di verifica**.

7. Il servizio di provisioning crea un **codice di verifica** che è possibile usare per convalidare la proprietà del certificato. Copiare il codice negli Appunti. 

   ![Verificare il certificato](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Firmare digitalmente il codice di verifica per creare un certificato di verifica

A questo punto, è necessario firmare il *codice di verifica* con la chiave privata associata al certificato della CA X.509, che genera una firma. Questa operazione, nota come [verifica del possesso](https://tools.ietf.org/html/rfc5280#section-3.1), ha come risultato un certificato di verifica firmato.

Microsoft offre strumenti ed esempi che facilitano la creazione di un certificato di verifica firmato: 

- **Azure IoT Hub C SDK** fornisce script PowerShell (Windows) e Bash (Linux) che consentono di creare più facilmente certificati della CA e foglia per lo sviluppo e per l'esecuzione della verifica del possesso tramite un codice di verifica. È possibile scaricare i [file](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) pertinenti al sistema in uso in una cartella di lavoro e seguire le istruzioni nel [file Leggimi Managing CA certificates](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) (Gestione di certificati della CA) per eseguire la verifica del possesso per un certificato della CA. 
- **Azure IoT Hub C# SDK** contiene l'[esempio di verifica del certificato di gruppo](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples/GroupCertificateVerificationSample), che è possibile usare per effettuare la verifica del possesso.
- È possibile seguire la procedura in nell'articolo [Script PowerShell per la gestione di certificati X.509 firmati da CA](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-security-x509-create-certificates) nella documentazione dell'hub IoT, in particolare lo script citato nella sezione [Verifica del possesso del certificato della CA X.509](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-security-x509-create-certificates#signverificationcode).
 
> [!IMPORTANT]
> Oltre a eseguire la verifica del possesso, gli script di PowerShell e Bash citati in precedenza consentono anche di creare certificati radice, certificati intermedi e certificati foglia utilizzabili per eseguire l'autenticazione e il provisioning di dispositivi. Questi certificati devono essere usati solo per lo sviluppo. Non devono mai essere usati in un ambiente di produzione. 

Gli script di PowerShell e Bash forniti nella documentazione e negli SDK si basano su [OpenSSL](https://www.openssl.org/). È anche possibile usare OpenSSL o altri strumenti di terze parti che consentono di eseguire la verifica del possesso. Per altre informazioni sugli strumenti forniti con gli SDK, vedere [Come usare gli strumenti disponibili negli SDK](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Caricare il certificato di verifica firmato

1. Caricare la firma risultante come certificato di verifica nel servizio di provisioning nel portale. In **Dettagli certificato** nel portale di Azure usare l'icona _Esplora file_ accanto al campo **File di certificato di verifica con estensione pem o cer.** per caricare il certificato di verifica firmato dal sistema.

2. Al termine del caricamento del certificato fare clic su **Verifica**. Lo **STATO** del certificato diventa **_Verificato_** nell'elenco **Esplora certificati**. Fare clic su **Aggiorna** se non viene aggiornato automaticamente.

   ![Caricare la verifica del certificato](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come usare il portale per creare un gruppo di registrazione, vedere [Gestione delle registrazioni dei dispositivi con il portale di Azure](how-to-manage-enrollments.md).
- Per informazioni su come usare gli SDK del servizio per creare un gruppo di registrazione, vedere [Gestione delle registrazioni dei dispositivi con gli SDK del servizio](how-to-manage-enrollments-sdks.md).










