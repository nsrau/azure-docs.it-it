---
title: Personalizzare l'interfaccia utente di Azure IoT Central Documenti Microsoft
description: Come personalizzare il tema e i collegamenti della Guida per l'applicazione centrale di Azure IoTHow to customize the theme and help links for your Azure IoT central application
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8f76f143b6c6a26b88b78e20d8d5d8ae1ae48553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158005"
---
# <a name="customize-the-azure-iot-central-ui"></a>Personalizzare l'interfaccia utente di Azure IoT CentralCustomize the Azure IoT Central UI

In questo articolo viene descritto come, in qualità di amministratore, è possibile personalizzare l'interfaccia utente dell'applicazione applicando temi personalizzati e modificando i collegamenti della Guida in modo che puntino alle risorse della Guida personalizzate. 



La schermata seguente mostra una pagina che usa il tema standard:The following screenshot shows a page using the standard theme:

![Tema Standard IoT Central](./media/howto-customize-ui/standard-ui.png)

La schermata seguente mostra una pagina usando una schermata personalizzata con gli elementi personalizzati dell'interfaccia utente evidenziati:The following screenshot shows a page using a custom screenshot with the customized UI elements highlighted:

![Tema IoT Central personalizzato](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Crea tema

Per creare un tema personalizzato, passare alla pagina **Personalizza l'applicazione** nella sezione **Amministrazione:To** create a custom theme, navigate to the Customize your application page in the Administration section:

![Temi ioT Central](./media/howto-customize-ui/themes.png)

In questa pagina è possibile personalizzare i seguenti aspetti dell'applicazione:

### <a name="application-logo"></a>Logo dell'applicazione

Un'immagine PNG, non superiore a 1 MB, con uno sfondo trasparente. Questo logo viene visualizzato a sinistra sulla barra del titolo dell'applicazione IoT Central.

Se l'immagine del logo include il nome dell'applicazione, è possibile nascondere il testo del nome dell'applicazione. Per ulteriori informazioni, vedere [Gestire l'applicazione](howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Icona del browser (favicon)

Un'immagine PNG, non più grande di 32 x 32 pixel, con uno sfondo trasparente. Un browser Web può utilizzare questa immagine nella barra degli indirizzi, nella cronologia, nei segnalibri e nella scheda del browser.

### <a name="browser-colors"></a>Colori del browser

È possibile modificare il colore dell'intestazione di pagina e il colore utilizzato per evidenziare i pulsanti e altre evidenziazioni. Utilizzare un valore di colore esadecimale di sei caratteri nel formato `##ff6347`. Per ulteriori informazioni sulla notazione colore **Valore HEX,** consultate [Colori HTML.](https://www.w3schools.com/html/html_colors.asp)

> [!NOTE]
> È sempre possibile ripristinare le opzioni predefinite nella pagina **Personalizza l'applicazione.**

### <a name="changes-for-operators"></a>Modifiche per gli operatori

Se un amministratore crea un tema personalizzato, gli operatori e altri utenti dell'applicazione non possono più scegliere un tema in **Impostazioni**.

## <a name="replace-help-links"></a>Sostituire i collegamenti della Guida

Per fornire informazioni personalizzate della Guida agli operatori e ad altri utenti, è possibile modificare i collegamenti nel menu **Guida** dell'applicazione.

Per modificare i collegamenti della Guida, passare alla pagina **Della Guida Personalizza** nella sezione **Amministrazione:**

![Personalizzare i collegamenti della Guida di IoT Central](./media/howto-customize-ui/help-links.png)

È inoltre possibile aggiungere nuove voci al menu guida e rimuovere le voci predefinite:

![Guida personalizzata di IoT Central](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> È sempre possibile ripristinare i collegamenti della Guida predefiniti nella pagina **di aiuto Personalizza.**

## <a name="next-steps"></a>Passaggi successivi

Ora che hai imparato a personalizzare l'interfaccia utente nell'applicazione IoT Central, ecco alcuni passaggi successivi suggeriti:

- [Amministrare l'applicazione](./howto-administer.md)
- [Aggiungere riquadri al dashboard](howto-add-tiles-to-your-dashboard.md)