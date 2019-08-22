---
title: Personalizzare l'interfaccia utente di Azure IoT Central | Microsoft Docs
description: Come personalizzare il tema e i collegamenti della Guida per l'applicazione Azure Internet delle cose centrale
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6d120ecc322318e6daf72c506131bcd85c3be4e5
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880852"
---
# <a name="customize-the-azure-iot-central-ui-preview-features"></a>Personalizzare l'interfaccia utente di Azure IoT Central (funzionalità di anteprima)

Questo articolo descrive come un amministratore può personalizzare l'interfaccia utente dell'applicazione applicando temi personalizzati e modificando i collegamenti della Guida in modo che puntino alle risorse della Guida personalizzate. 

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Lo screenshot seguente mostra una pagina che usa il tema standard:

![Tema IoT Central Standard](./media/howto-customize-ui-pnp/standard-ui.png)

Lo screenshot seguente mostra una pagina che usa uno screenshot personalizzato con gli elementi dell'interfaccia utente personalizzati evidenziati:

![Tema IoT Central personalizzato](./media/howto-customize-ui-pnp/themed-ui.png)

## <a name="create-theme"></a>Crea tema

Per creare un tema personalizzato, passare alla pagina **personalizzare l'applicazione** nella sezione **Amministrazione** :

![Temi IoT Central](./media/howto-customize-ui-pnp/themes.png)

In questa pagina è possibile personalizzare i seguenti aspetti dell'applicazione:

### <a name="application-logo"></a>Logo dell'applicazione

Immagine PNG, non superiore a 1 MB, con uno sfondo trasparente. Questo logo viene visualizzato a sinistra sulla barra del titolo dell'applicazione IoT Central.

Se l'immagine del logo include il nome dell'applicazione, è possibile nascondere il testo del nome dell'applicazione. Per altre informazioni, vedere [gestire l'applicazione](./howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Icona del browser (favicon)

Immagine PNG, non superiore a 32 x 32 pixel, con uno sfondo trasparente. Un Web browser può usare questa immagine nella scheda della barra degli indirizzi, della cronologia, dei segnalibri e del browser.

### <a name="browser-colors"></a>Colori del browser

È possibile modificare il colore dell'intestazione di pagina e il colore utilizzato per i pulsanti di accento e altre evidenziazioni. Usare un valore di colore esadecimale a sei caratteri `##ff6347`nel formato. Per ulteriori informazioni sulla notazione dei colori del **valore esadecimale** , vedere [colori HTML](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> È sempre possibile ripristinare le opzioni predefinite nella pagina **Personalizza applicazione** .

### <a name="changes-for-operators"></a>Modifiche per gli operatori

Se un amministratore crea un tema personalizzato, gli operatori e gli altri utenti dell'applicazione non potranno più scegliere un tema in **Impostazioni**.

## <a name="replace-help-links"></a>Sostituisci collegamenti della Guida

Per fornire informazioni sulla guida personalizzate per gli operatori e altri utenti, è possibile modificare i collegamenti nel menu della **Guida** dell'applicazione.

Per modificare i collegamenti della guida, passare alla pagina **Personalizza guida** nella sezione **Amministrazione** :

![Personalizzare i collegamenti della Guida IoT Central](./media/howto-customize-ui-pnp/help-links.png)

È anche possibile aggiungere nuove voci al menu della guida e rimuovere le voci predefinite:

![Guida IoT Central personalizzata](./media/howto-customize-ui-pnp/custom-help.png)

> [!NOTE]
> È sempre possibile ripristinare i collegamenti della Guida predefiniti nella pagina **Personalizza guida** .

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come personalizzare l'interfaccia utente nell'applicazione IoT Central, ecco alcuni passaggi successivi suggeriti:

- [Amministrare l'applicazione](./howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- [Configurare il dashboard dell'applicazione](./howto-configure-homepage.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)