---
title: Personalizzare l'interfaccia utente di Azure IoT Central | Microsoft Docs
description: Come personalizzare i collegamenti di tema e della Guida per l'applicazione di Azure IoT central
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4d385f1e8c883453b4153ca4c9119d3be0a608bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495567"
---
# <a name="customize-the-azure-iot-central-ui"></a>Personalizzare l'interfaccia utente di Azure IoT Central 

*Questo articolo si applica agli amministratori.*

IoT Central consente di personalizzare l'interfaccia utente dell'applicazione, applicazione di temi personalizzati e modificando i collegamenti della Guida in modo da puntare alle risorse di file della Guida personalizzato. Lo screenshot seguente mostra una pagina con il tema standard:

![Tema standard IoT Central](./media/howto-customize-ui/standard-ui.png)

Lo screenshot seguente mostra una pagina con uno screenshot personalizzato con elementi dell'interfaccia utente personalizzati evidenziati:

![Tema centrale di IoT personalizzato](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Creazione del tema

Per creare un tema personalizzato, passare al **personalizzare l'applicazione** pagina il **amministrazione** sezione:

![Temi IoT Central](./media/howto-customize-ui/themes.png)

In questa pagina, è possibile personalizzare i seguenti aspetti dell'applicazione:

### <a name="application-logo"></a>Logo dell'applicazione

Un'immagine PNG non superiore a 1 MB, con uno sfondo trasparente. Questo logo viene visualizzato a sinistra sulla barra del titolo dell'applicazione IoT Central.

Se l'immagine del logo sono inclusi il nome dell'applicazione, è possibile nascondere il testo del nome dell'applicazione. Per altre informazioni, vedere [gestire le impostazioni dell'applicazione](./howto-administer.md#manage-application-settings).

### <a name="browser-icon-favicon"></a>Icona di esplorazione (favicon)

Un'immagine PNG non maggiore di 32 x 32 pixel, con uno sfondo trasparente. Un web browser è possibile usare questa immagine in cui la barra degli indirizzi, cronologia, i segnalibri e scheda del browser.

### <a name="browser-colors"></a>Colori del browser

È possibile modificare il colore dell'intestazione di pagina e il colore utilizzato per accenti pulsanti e altri dati principali. Usare un valore di colore in formato esadecimale di sei caratteri nel formato `##ff6347`. Per altre informazioni sulle **valore ESADECIMALE** notazione dei colori, vedere [colori HTML](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> È sempre possibile tornare indietro per le opzioni predefinite di **personalizzare l'applicazione** pagina.

### <a name="changes-for-operators"></a>Modifiche per gli operatori

Se un amministratore crea un tema personalizzato, quindi gli operatori e altri utenti dell'applicazione non è più possono scegliere un tema in **impostazioni**.

## <a name="replace-help-links"></a>Sostituire i collegamenti della Guida

Per fornire le informazioni della Guida personalizzata per gli operatori e altri utenti, è possibile modificare i collegamenti nell'applicazione **aiutare** menu.

Per modificare i collegamenti della Guida, passare al **personalizzare help** pagina il **amministrazione** sezione:

![Personalizzare i collegamenti della Guida IoT Central](./media/howto-customize-ui/help-links.png)

È anche possibile aggiungere nuove voci al menu della Guida in linea e rimuovere le voci predefinite:

![Assistenza personalizzata IoT Central](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> È sempre possibile tornare indietro ai collegamenti della Guida predefinito di **personalizzare help** pagina.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come personalizzare l'interfaccia utente nell'applicazione IoT Central, ecco alcuni passaggi successivi consigliati:

- [Amministrare l'applicazione](./howto-administer.md)
- [Configurare il dashboard dell'applicazione](./howto-configure-homepage.md)