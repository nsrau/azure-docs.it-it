---
title: Come configurare un servizio cloud (portale) | Microsoft Docs
description: Informazioni su come configurare un servizio cloud in Azure. Informazioni su come aggiornare la configurazione del servizio cloud e configurare l'accesso remoto per le istanze del ruolo. Questi esempi utilizzano il portale di Azure.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: adegeo
ms.openlocfilehash: 255e496881f6269d37d3b2d982ba31861458631c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="how-to-configure-cloud-services"></a>Come configurare i servizi cloud

È possibile configurare le impostazioni più comuni di un servizio cloud nel portale di Azure. In alternativa, se si preferisce aggiornare direttamente i file di configurazione, scaricare un file di configurazione del servizio da aggiornare, quindi caricare il file aggiornato e aggiornare il servizio cloud con le modifiche apportate alla configurazione. In ogni caso, per gli aggiornamenti della configurazione viene effettuato il push in tutte le istanze del ruolo.

È anche possibile gestire le istanze dei ruoli del servizio cloud o creare una connessione Desktop remoto per tali servizi.

Azure può garantire il 99,95 di disponibilità del servizio durante gli aggiornamenti della configurazione solo se si dispone di almeno due istanze del ruolo per ogni ruolo. In questo modo, una macchina virtuale può elaborare le richieste dei client mentre l'altra viene aggiornata. Per altre informazioni, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Modificare un servizio cloud

Dopo aver aperto il [portale di Azure](https://portal.azure.com/), passare al servizio cloud. Da qui è possibile gestire molti aspetti.

![Pagina Impostazioni](./media/cloud-services-how-to-configure-portal/cloud-service.png)

I collegamenti **Impostazioni** o **Tutte le impostazioni** consentono di accedere a **Impostazioni** dove è possibile modificare le **proprietà** e la **configurazione**, gestire i **certificati**, configurare le **regole di avviso** e gestire gli **utenti** che hanno accesso a questo servizio cloud.

![Impostazioni del servizio cloud di Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Gestire la versione del sistema operativo guest

Per impostazione predefinita, Azure aggiorna periodicamente il sistema operativo guest all'immagine supportata più recente nella famiglia di sistemi operativi specificata nella configurazione del servizio (file con estensione cscfg), ad esempio Windows Server 2016.

Se è necessario fare riferimento a una versione specifica del sistema operativo, è possibile configurarla in **Configurazione**.

![Configurare la versione del sistema operativo](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> La scelta di una versione specifica del sistema operativo comporta la disabilitazione degli aggiornamenti automatici del sistema operativo e rende l'applicazione di patch a carico dell'utente. È necessario assicurarsi che le istanze del ruolo ricevano gli aggiornamenti. In caso contrario, si rischia l'esposizione dell'applicazione a vulnerabilità della sicurezza.

## <a name="monitoring"></a>Monitoraggio

È possibile aggiungere avvisi al servizio cloud. Fare clic su **Impostazioni** > **Regole di avviso** > **Aggiungi avviso**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Da qui è possibile configurare un avviso. La casella di riepilogo a discesa **Metrica** consente di configurare un avviso per i tipi di dati seguenti.

* Lettura disco
* Scrittura disco
* Rete in ingresso
* Rete in uscita
* Percentuale CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configurazione del monitoraggio da un riquadro della metrica

Invece di usare **Impostazioni** > **Regole di avviso** è possibile fare clic su uno dei riquadri metrici nella sezione **Monitoraggio** del servizio cloud.

![Monitoraggio del servizio cloud](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Da qui è possibile personalizzare il grafico usato con il riquadro oppure aggiungere una regola di avviso.

## <a name="reboot-reimage-or-remote-desktop"></a>Riavviare il computer, ricreare l'immagine o creare una connessione Desktop remoto

È possibile configurare il desktop remoto tramite il [portale di Azure (configurare il desktop remoto)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) o tramite [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Per riavviare il computer, ricreare l'immagine o accedere in remoto a un servizio cloud, selezionare l'istanza del servizio cloud.

![Istanza del servizio cloud](./media/cloud-services-how-to-configure-portal/cs-instance.png)

È possibile avviare una connessione Desktop remoto, riavviare l'istanza o ricrearne l'immagine in remoto (iniziare con un'immagine aggiornata).

![Pulsanti di istanza del servizio cloud](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Riconfigurare il file con estensione cscfg

Potrebbe essere necessario riconfigurare il servizio cloud con il file di [configurazione del servizio (CSCFG)](cloud-services-model-and-package.md#cscfg). È prima necessario scaricare il file con estensione cscfg, modificarlo, quindi caricarlo.

1. Fare clic sull'icona **Impostazioni** o sul collegamento **Tutte le impostazioni** per aprire **Impostazioni**.

    ![Pagina Impostazioni](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Fare clic sull’elemento **Configurazione** .

    ![Blade Configurazione](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Fare clic sul pulsante **Download** .

    ![Download](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Dopo aver aggiornato il file di configurazione del servizio, caricare e applicare gli aggiornamenti della configurazione:

    ![Caricamento](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Selezionare il file con estensione cscfg e fare clic su **OK**.

## <a name="next-steps"></a>Passaggi successivi

* Procedura [distribuire un servizio cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurare un [nome di dominio personalizzato](cloud-services-custom-domain-name-portal.md).
* [Gestire il servizio cloud](cloud-services-how-to-manage-portal.md).
* Configurare i [certificati ssl](cloud-services-configure-ssl-certificate-portal.md).
