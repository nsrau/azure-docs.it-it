---
title: Programma Azure Certified Device | Microsoft Docs
description: Informazioni sul programma Azure Certified Device Program.
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1fcfc7a9e632e5db1fb809dba7a938c8641c9ddc
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048101"
---
# <a name="what-is-the-azure-certified-device-program"></a>Che cos'è il programma Azure Certified Device?

Il programma Azure Certified Device garantisce che le soluzioni dei clienti funzionino senza interruzioni con i servizi di Azure. Il programma usa strumenti, servizi e un Marketplace per condividere le conoscenze e le procedure consigliate del settore con una community di generatori di dispositivi e soluzioni.

Questo programma è progettato per:

- **Dare confidenza ai clienti:** I clienti possono acquistare con fiducia i dispositivi certificati da Microsoft per l'uso con i servizi di Azure.

- **Aiutare i clienti a trovare i dispositivi giusti per la soluzione:** I creatori di dispositivi possono pubblicare le funzionalità univoche dei propri dispositivi come parte del processo di certificazione. I clienti possono trovare facilmente i prodotti che soddisfano le proprie esigenze.

- **Promuovere i dispositivi certificati:** I generatori di dispositivi ottengono maggiore visibilità, contatti con i clienti e utilizzo del marchio Azure Certified Device.

Questo articolo illustra come:

- Eseguire l'onboarding dell'azienda nel programma Azure Certified Device.
- Determinare quale certificazione è applicabile al dispositivo.
- Individuare i requisiti del programma e altre risorse per iniziare a eseguire il test.
- Usare il portale per dispositivi certificati di Azure per convalidare il dispositivo.

## <a name="onboarding"></a>Onboarding

Per certificare i dispositivi nel [portale del dispositivo Azure Certified](https://aka.ms/acdp), è necessario completare i passaggi seguenti:

1. Assicurarsi che la società disponga di un account Azure Active Directory usando un tenant aziendale o dell'Istituto di istruzione.
2. Unisciti al [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) usando il tuo account.
3. Accedere al portale per [dispositivi certificati di Azure](https://aka.ms/acdp) dopo aver aggiunto a MPN.
4. Esaminare e firmare il [contratto di programma](https://aka.ms/acdagreement) nella pagina profilo società

### <a name="company-profile"></a>Profilo della società

Per gestire il profilo della società nel portale per i dispositivi Azure Certified, selezionare **Profilo azienda**. Il profilo della società include l'URL, l'indirizzo di posta elettronica e il logo della società. Accettare il contratto di programma in questa pagina prima di continuare con le operazioni di certificazione.

La pagina di descrizione del dispositivo nel catalogo dispositivi certificati Azure usa le informazioni sul profilo della società.

## <a name="choose-the-certification"></a>Scegliere la certificazione

Esistono tre diverse certificazioni, ognuna delle quali si concentra sulla distribuzione di un valore cliente diverso. A seconda del tipo di dispositivo che si sta creando e dei destinatari, è possibile scegliere la certificazione o le certificazioni più idonee:

### <a name="azure-certified-device"></a>Dispositivo Azure Certified

_Azure Certified Device Certificates_ verifica che un dispositivo possa connettersi con l'hub Azure e il provisioning sicuro tramite il servizio Device provisioning (DPS). Questa certificazione riflette la funzionalità e l'interoperabilità di un dispositivo che è una linea di base necessaria per certificazioni più avanzate.

- Per ulteriori informazioni, vedere i [requisiti di certificazione](https://aka.ms/acdrequirements).
- Per altre informazioni sull'uso di DPS per connettere il dispositivo all'hub Azure, vedere la [Panoramica dei dispositivi di provisioning](../iot-dps/about-iot-dps.md).

### <a name="iot-plug-and-play"></a>Plug and Play IoT

Internet delle cose _plug and play certificazione_, una certificazione incrementale per la certificazione dei dispositivi certificati di Azure, semplifica il processo di creazione di dispositivi senza codice del dispositivo personalizzato. Internet delle cose Plug and Play consente ai partner hardware di creare dispositivi facilmente integrabili con soluzioni cloud basate su IoT Central di Azure e soluzioni di terze parti.

- Per ulteriori informazioni, vedere i [requisiti di certificazione](https://aka.ms/acdiotpnprequirements).
- Per altre informazioni su come preparare un dispositivo per i test Plug and Play, vedere [come certificare i dispositivi Plug and Play](howto-certify-device.md).

### <a name="edge-managed"></a>Gestione Edge

_Certificazione gestita da Edge_, una certificazione incrementale per la certificazione dei dispositivi certificati di Azure, è incentrata sugli standard di gestione dei dispositivi per i dispositivi Azure Internet che eseguono Windows, Linux o RTO. Attualmente questa certificazione del programma è incentrata sulla compatibilità del runtime perimetrale per la distribuzione e la gestione dei moduli.

> [!TIP]
> Questo programma era noto in precedenza come _programma di certificazione IOT Edge_.

- Per ulteriori informazioni, vedere i [requisiti di certificazione](https://aka.ms/acdedgemanagedrequirements).
- Per altre informazioni sulle IoT Edge, vedere [Panoramica di IOT Edge](../iot-edge/about-iot-edge.md).
- Per ulteriori informazioni sui sistemi operativi e i contenitori supportati, vedere [IOT Edge sistemi supportati](../iot-edge/support.md).

## <a name="use-the-azure-certified-device-portal"></a>Usare il portale per dispositivi Azure Certified

Questa sezione riepiloga come usare il portale per i [dispositivi Azure Certified](https://certify.azure.com). Per ulteriori informazioni, vedere la [Guida introduttiva al portale](https://aka.ms/acdhelp).

Per certificare un dispositivo nel programma Azure Certified Device, completare i quattro passaggi seguenti:

1. Specificare i dettagli del dispositivo
2. Testare il dispositivo
3. Invia e completa la revisione
4. Pubblicare nel catalogo di dispositivi certificati Azure (facoltativo)

### <a name="provide-device-details"></a>Specificare i dettagli del dispositivo

Per ogni dispositivo che si vuole certificare, usare i moduli nel portale di certificazione per registrare i dettagli sull'hardware del dispositivo, le istruzioni di installazione e il materiale di marketing:

- **Informazioni sul dispositivo:** Raccoglie informazioni sul dispositivo, ad esempio il nome, la descrizione, i dettagli dell'hardware e il sistema operativo.
- **Guida introduttiva**: un documento PDF che può essere usato da un cliente per usare rapidamente il prodotto. Sono disponibili [modelli di esempio](https://aka.ms/GSTemplate) .
- **Dettagli marketing:** Fornire informazioni di marketing pronte per il cliente per il dispositivo, ad esempio informazioni sulle immagini e sul database di distribuzione.
- **Certificazioni di settore aggiuntive:** Una sezione facoltativa che consente di fornire informazioni su qualsiasi altra certificazione del dispositivo.

### <a name="test-the-device"></a>Testare il dispositivo

Questa fase interagisce con il dispositivo ed esegue una serie di test dopo che il dispositivo usa DPS per connettersi all'hub Internet. Al termine, è possibile visualizzare un set di file di log con i risultati del test del dispositivo.

Nel portale di certificazione sono disponibili istruzioni su come connettersi all'istanza dell'hub Internet delle cose usata per il test. È possibile stabilire la connessione DPS mediante uno dei [metodi di attestazione supportati](../iot-dps/concepts-service.md#attestation-mechanism).

Il team di dispositivi certificati di Azure può contattare il generatore di dispositivi per ulteriore convalida manuale del dispositivo.

### <a name="submit-and-publish"></a>Inviare e pubblicare

La fase finale obbligatoria consiste nell'inviare il progetto per la revisione. Questo passaggio invia una notifica a un membro del team di Azure Certified per verificare la completezza del progetto, inclusi i dettagli sul dispositivo e sul marketing, e la Guida introduttiva. Un membro del team può contattare l'utente all'indirizzo di posta elettronica della società fornito in precedenza con domande o modificare richieste prima dell'approvazione.

Se il dispositivo richiede ulteriore convalida manuale come parte della certificazione, si riceverà una notifica al momento.

Quando un dispositivo è certificato, è possibile scegliere di pubblicare i dettagli del prodotto nel catalogo dei dispositivi certificati di Azure usando la funzionalità **pubblica nel catalogo** nella pagina di riepilogo del prodotto.

## <a name="if-you-have-questions"></a>In caso di domande

Contattare il team in [iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question) caso di domande sui programmi di certificazione, il portale di certificazione o il catalogo dei dispositivi Azure Certified.

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di una panoramica del programma Azure Certified Device, un passaggio successivo suggerito consiste nell'apprendere [come certificare i dispositivi Plug and Play](howto-certify-device.md).