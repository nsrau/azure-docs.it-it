<properties
   pageTitle="Creare un probe personalizzato per un gateway applicazione con il portale | Microsoft Azure"
   description="Informazioni su come creare un probe personalizzato per un gateway applicazione usando il portale"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>  
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace" />

# Creare un probe personalizzato per un gateway applicazione con il portale

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-create-probe-portal.md)
- [PowerShell per Azure Resource Manager](application-gateway-create-probe-ps.md)
- [PowerShell per Azure classico](application-gateway-create-probe-classic-ps.md)

<BR>  

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## Scenario

Lo scenario seguente illustra la creazione di un probe di integrità personalizzato in un gateway applicazione esistente. Lo scenario presuppone che sia già stata seguita la procedura per [creare un gateway applicazione](application-gateway-create-gateway-portal.md).

## <a name="createprobe"></a>Creare il probe

I probe vengono configurati con un processo in due passaggi nel portale. Il primo passaggio consiste nel creare il probe, quindi si aggiunge il probe alle impostazioni HTTP back-end del gateway applicazione.

### Passaggio 1

Passare a http://portal.azure.com e selezionare un gateway applicazione esistente.

![Panoramica del gateway applicazione][1]  

### Passaggio 2

Fare clic su **Probe** e quindi sul pulsante **Aggiungi** per aggiungere un nuovo probe.

![Pannello Aggiungi probe con informazioni inserite][2]  

### Passaggio 3

Inserire le informazioni necessarie per il probe e al termine fare clic su **OK**.

- **Nome**: nome descrittivo del probe accessibile nel portale.
- **Host**: nome host usato per il probe.
- **Percorso**: parte restante dell'URL completo per il probe personalizzato.
- **Intervallo (sec)**: frequenza con cui il probe viene eseguito per controllare l'integrità.
- **Timeout (sec)**: durata dell'attesa prima che si verifichi il timeout del probe.
- **Soglia di non integrità**: numero di tentativi non riusciti da considerare come uno stato di non integrità.

> [AZURE.IMPORTANT] Il nome host non è il nome del server. È il nome dell'host virtuale in esecuzione nel server applicazioni. Il probe viene inviato a http://(host nome):(porta da impostazioni HTTP)/percorsoURL

![Impostazioni di configurazione del probe][3]  

## Aggiungere il probe al gateway

Ora che il probe è stato creato, deve essere aggiunto al gateway. Le impostazioni del probe vengono definite nelle impostazioni HTTP back-end del gateway applicazione.

### Passaggio 1

Fare clic su **Impostazioni HTTP** nel gateway applicazione e quindi sulle impostazioni HTTP back-end correnti nella finestra per visualizzare il pannello di configurazione.

![Finestra delle impostazioni HTTP][4]  

### Passaggio 2

Nel pannello delle impostazioni **appGatewayBackEndHttp** fare clic su **Usa probe personalizzato** e scegliere il probe creato nella sezione [Creare il probe](#createprobe). Al termine, fare clic su **OK** per applicare le impostazioni.

![Pannello delle impostazioni appGatewayBackEndHttp][5]  

Il probe predefinito controlla l'accesso predefinito all'applicazione Web. Ora che è stato creato un probe personalizzato, il gateway applicazione usa il percorso personalizzato definito per monitorare l'integrità per il back-end selezionato. In base ai criteri definiti, il gateway applicazione controlla il file specificato nel probe. Se la chiamata a host:Porta/percorso non restituisce una risposta di stato HTTP 200 OK, dopo che è stata raggiunta la soglia di non integrità il server viene escluso dalla rotazione. Il probe continua a essere eseguito sull'istanza non integra per determinare quando risulterà di nuovo integra. Quando l'istanza viene nuovamente aggiunta al pool di server integri, il flusso del traffico verso l'istanza riprenderà e il relativo probe verrà eseguito all'intervallo normale specificato dall'utente.


## Passaggi successivi

Per informazioni su come configurare l'offload SSL con un gateway applicazione di Azure, vedere [Configurare un gateway applicazione per l'offload SSL con il portale](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png

<!---HONumber=AcomDC_0810_2016-->