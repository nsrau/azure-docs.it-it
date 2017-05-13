## <a name="overview"></a>Panoramica

In questa esercitazione si completa questa procedura:

- Distribuire un'istanza della soluzione preconfigurata di monitoraggio remoto nella sottoscrizione di Azure. Questo passaggio distribuisce e configura automaticamente più servizi di Azure.
- Configurare il dispositivo per la comunicazione con il computer e la soluzione di monitoraggio remoto.
- Aggiornare il codice del dispositivo di esempio per la connessione alla soluzione di monitoraggio remoto e inviare i dati di telemetria simulati che è possibile visualizzare nel dashboard della soluzione.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

> [!NOTE]
> Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].

### <a name="required-software"></a>Requisiti software

È necessario un client SSH nel computer desktop per poter accedere in remoto alla riga di comando in Raspberry Pi.

- Windows non include un client SSH. È consigliabile usare [PuTTY](http://www.putty.org/).
- La maggior parte delle distribuzioni Linux e Mac OS includono l'utilità SSH della riga di comando. Per altre informazioni, vedere [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (SSH con Linux o Mac OS).

### <a name="required-hardware"></a>Requisiti hardware

Un computer desktop per potersi connettere in remoto alla riga di comando in Raspberry Pi.

[Starter kit di Microsoft Azure IoT per Raspberry Pi 3][lnk-starter-kits] o componenti equivalenti. Questa esercitazione usa gli elementi seguenti del kit:

- Raspberry Pi 3
- Scheda microSD (con NOOBS)
- Un cavo USB mini
- Un cavo Ethernet

[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/