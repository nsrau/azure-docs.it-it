---
title: Creare un'applicazione Azure IoT Central | Microsoft Docs
description: Come creare un'applicazione Azure IoT Central in qualità di amministratore.
author: tbhagwat3
ms.author: tanmayb
ms.date: 03/20/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 39992a1cc36d00a64ee6430cad5f24af3e1e1157
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629287"
---
# <a name="create-your-azure-iot-central-application"></a>Creare l'applicazione Azure IoT Central

L'applicazione Microsoft Azure IoT Central si crea dalla pagina [Create Application](https://apps.microsoftiotcentral.com/create) (Crea applicazione). Per creare un'applicazione Azure IoT Central è necessario completare tutti i campi in questa pagina e quindi scegliere **Create** (Crea). Questo articolo offre maggiori informazioni su ognuno dei campi.

![La pagina Create Application](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>Piano di pagamento

È possibile creare una versione di valutazione o un'applicazione a pagamento. Altre informazioni sulle applicazioni in versione di valutazione e a pagamento sono disponibili in questa pagina.

## <a name="application-name"></a>Nome dell'applicazione

Il nome dell'applicazione è mostrato sulla pagina **Application Manager** e in ogni applicazione Azure IoT Central. È possibile scegliere qualsiasi nome per l'applicazione Azure IoT Central. Scegliere un nome significativo per sé e per tutte le persone dell'organizzazione.

## <a name="application-url"></a>URL applicazione

L'URL dell'applicazione è il collegamento all'applicazione. È possibile salvare un segnalibro nel browser o condividerlo con altri utenti.

Quando si immette il nome dell'applicazione, l'URL dell'applicazione viene generato automaticamente. Se si preferisce, è possibile scegliere un URL diverso per l'applicazione. Ogni URL di Azure IoT Central deve essere univoco. Se l'URL scelto è già in uso, compare un messaggio di errore.

## <a name="directory"></a>Directory

Solo nelle applicazioni a pagamento.

Scegliere un tenant di Azure Active Directory per creare un'applicazione Azure IoT Central. Un tenant di Azure Active Directory contiene le identità degli utenti, le credenziali e altre informazioni sull'organizzazione. Più sottoscrizioni di Azure possono essere associate a un singolo tenant di Azure Active Directory.

Se non si dispone di un tenant di Azure Active Directory, ne viene creato automaticamente uno quando si crea una sottoscrizione di Azure.

Per altre informazioni, vedere [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Sottoscrizione di Azure

Una sottoscrizione di Azure consente di creare istanze dei servizi di Azure. Azure IoT Central rileva automaticamente tutte le sottoscrizioni di Azure a cui è possibile accedere e le mostra in un elenco a discesa nella pagina **Create Application** (Crea applicazione). Scegliere una sottoscrizione di Azure per creare una nuova applicazione Azure IoT Central.

Se non si ha una sottoscrizione di Azure, è possibile crearne una in questa pagina. Dopo aver creato la sottoscrizione di Azure, passare alla pagina **Create Application** (Crea applicazione). Selezionare la sottoscrizione nella casella di riepilogo a discesa **Azure Subscription** (Sottoscrizione di Azure).

Per altre informazioni, vedere la documentazione sulle [sottoscrizioni di Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Region

Solo nelle applicazioni a pagamento.

Scegliere l'area in cui si vuole creare l'applicazione Azure IoT Central. In genere è consigliabile scegliere l'area più vicina fisicamente ai dispositivi per ottenere prestazioni ottimali.

Per altre informazioni, vedere la documentazione sulle [aree di Azure](https://docs.microsoft.com/en-us/azure/guides/developer/azure-developer-guide#azure-regions).

È possibile vedere le aree in cui Azure IoT Central è disponibile nella pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

> [!Note]
> Dopo aver scelto un'area, è possibile spostare l'applicazione in un'altra area geografica in un secondo momento.

## <a name="application-template"></a>Modello di applicazione

Per la nuova applicazione Azure IoT Central è possibile scegliere uno dei modelli di applicazione disponibili. Un modello di applicazione può contenere elementi predefiniti, ad esempio modelli di dispositivi e dashboard, che facilitano le cose:

| Modello di applicazione | DESCRIZIONE |
| -------------------- | ----------- |
| Applicazione personalizzata   | Crea un'applicazione vuota per l'utente da popolare con i propri modelli di dispositivi e dispositivi. |
| Esempio Contoso       | Crea un'applicazione che include un modello di dispositivo per un dispositivo connesso semplice. Usare questo modello per iniziare a esplorare Azure IoT Central. |
| Esempio Devkits       | Crea un'applicazione con modelli di dispositivo pronti per la connessione a un dispositivo MXChip o Raspberry Pi. Si consiglia di usare questo modello agli sviluppatori di dispositivi che vogliono sperimentare il codice in uno di questi dispositivi. |

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per creare un'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)