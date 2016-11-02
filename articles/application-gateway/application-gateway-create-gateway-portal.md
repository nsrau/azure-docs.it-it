<properties
   pageTitle="Creare un gateway applicazione con il portale | Microsoft Azure"
   description="Informazioni su come creare un gateway applicazione usando il portale"
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
   ms.date="09/09/2016"
   ms.author="gwallace" />


# <a name="create-an-application-gateway-by-using-the-portal"></a>Creare un gateway applicazione con il portale

Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. Il gateway applicazione offre numerose funzionalità di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller), tra cui bilanciamento del carico HTTP, affinità di sessione basata su cookie, offload SSL (Secure Sockets Layer), probe di integrità personalizzati, supporto per più siti e molte altre. Per un elenco completo delle funzionalità supportate, vedere [Panoramica del gateway applicazione](application-gateway-introduction.md)

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-create-gateway-portal.md)
- [PowerShell per Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [PowerShell per Azure classico](application-gateway-create-gateway.md)
- [Modello di Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
- [Interfaccia della riga di comando di Azure](application-gateway-create-gateway-cli.md)

## <a name="scenario"></a>Scenario

Questo scenario mostra come creare un gateway applicazione usando il portale di Azure.

Questo scenario illustrerà come:

- Creare un gateway applicazione Medium con due istanze.
- Creare una rete virtuale denominata AdatumAppGatewayVNET con un blocco CIDR riservato 10.0.0.0/16.
- Creare una subnet denominata Appgatewaysubnet che usa 10.0.0.0/28 come blocco CIDR.
- Configurare un certificato per l'offload SSL.

![Esempio dello scenario][scenario]

>[AZURE.IMPORTANT] La configurazione aggiuntiva del gateway applicazione, che include i probe di integrità personalizzati, gli indirizzi del pool back-end e le regole aggiuntive, viene definita dopo la configurazione del gateway applicazione e non durante la distribuzione iniziale.

## <a name="before-you-begin"></a>Prima di iniziare

Il gateway applicazione di Azure richiede una propria subnet. Quando si crea una rete virtuale, assicurarsi di lasciare uno spazio indirizzi sufficiente per più subnet. Dopo che un gateway applicazione è stato distribuito in una subnet, alla subnet possono essere aggiunti solo altri gateway applicazione.

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

### <a name="step-1"></a>Passaggio 1

Passare al portale di Azure e fare clic su **Nuovo** > **Rete** > **Gateway applicazione**

![Creazione di un gateway applicazione][1]

### <a name="step-2"></a>Passaggio 2

Inserire quindi le informazioni di base sul gateway applicazione. Al termine, fare clic su **OK**

Per le impostazioni di base sono necessarie le informazioni seguenti.

- **Nome** : nome del gateway applicazione.
- **Dimensioni SKU**: la dimensione del gateway applicazione. Le opzioni disponibili sono Small, Medium e Large.
- **Numero di istanze**: il numero di istanze. Questo valore deve essere un numero compreso tra 2 e 10.
- **Gruppo di risorse**: il gruppo di risorse in cui includere il gateway applicazione. Può essere un gruppo di risorse esistente o nuovo.
- **Località**: l'area del gateway applicazione. È la stessa località del gruppo di risorse. *La località è importante perché la rete virtuale e l'IP pubblico devono trovarsi nella stessa località del gateway*.

![Pannello con impostazioni di base][2]

>[AZURE.NOTE] A scopo di test si può scegliere 1 come numero di istanze. È importante sapere che un numero di istanze inferiore a due non è coperto dal contratto di servizio e non è quindi consigliabile. È opportuno usare gateway Small a scopo di sviluppo/test e non per la produzione.

### <a name="step-3"></a>Passaggio 3

Dopo che sono state definite le impostazioni di base, il passaggio successivo consiste nel definire la rete virtuale da usare. La rete virtuale ospita l'applicazione per cui il gateway applicazione esegue il bilanciamento del carico.

Fare clic su **Scegliere una rete virtuale** per configurare la rete virtuale.

![Pannello con impostazioni per il gateway applicazione][3]

### <a name="step-4"></a>Passaggio 4

Nel pannello *Scegli rete virtuale* fare clic su **Crea nuovo**

Anche se non illustrato in questo scenario, a questo punto si potrebbe selezionare una rete virtuale esistente.  Se si usa una rete virtuale esistente, è importante sapere che, per poter essere usata, la rete virtuale deve avere una subnet vuota o una subnet contenente solo risorse gateway applicazione.

![Pannello Scegli rete virtuale][4]

### <a name="step-5"></a>Passaggio 5

Inserire le informazioni relative alla rete nel pannello **Crea rete virtuale** come descritto nella precedente sezione [Scenario](#scenario) .

![Pannello Crea rete virtuale con informazioni immesse][5]

### <a name="step-6"></a>Passaggio 6

Dopo che è stata creata la rete virtuale, il passaggio successivo consiste nel definire l'IP front-end per il gateway applicazione. A questo punto, è possibile scegliere per il front-end un indirizzo IP pubblico o privato. La scelta varia a seconda che si tratti di un'applicazione con connessione Internet o solo interna. Questo scenario presuppone l'uso di un indirizzo IP pubblico. Per scegliere un indirizzo IP privato si può fare clic sul pulsante **Privato** . È possibile scegliere un indirizzo IP assegnato automaticamente oppure selezionare la casella di controllo **Scegliere un indirizzo IP privato specifico** per immetterne uno manualmente.

### <a name="step-7"></a>Passaggio 7

Fare clic su **Scegliere un indirizzo IP pubblico**. Se è disponibile un indirizzo IP pubblico esistente, a questo punto è possibile sceglierlo. In questo scenario verrà creato un nuovo indirizzo IP pubblico. Fare clic su **Create new**

![Pannello Scegli indirizzo IP pubblico][6]

### <a name="step-8"></a>Passaggio 8

Assegnare quindi un nome descrittivo all'indirizzo IP pubblico e fare clic su **OK**

![Pannello Crea indirizzo IP pubblico][7]

### <a name="step-9"></a>Passaggio 9:

L'ultima impostazione da definire durante la creazione di un gateway applicazione è la configurazione del listener.  Se si usa **HTTP**, non è necessario configurare altro e si può fare clic su **OK**. Per usare **HTTPS** sono necessarie operazioni di configurazione aggiuntive.

Per usare **HTTPS**è necessario un certificato. Poiché è necessaria la chiave privata del certificato, occorre specificare un file di esportazione del certificato con estensione .pfx e la password.

### <a name="step-10"></a>Passaggio 10

Fare clic su **HTTPS** e quindi sull'icona a forma di **cartella** accanto alla casella di testo **Carica certificato PFX**.
Passare al file PFX del certificato nel file system. Dopo averlo selezionato, assegnare al certificato un nome descrittivo e digitare la password per il file PFX.

Al termine, fare clic su **OK** per rivedere le impostazioni del gateway applicazione.

![Sezione Configurazione listener nel pannello Impostazioni][9]

### <a name="step-11"></a>Passaggio 11

Esaminare la pagina di riepilogo e fare clic su **OK**.  Il gateway applicazione verrà inserito in coda e creato.

### <a name="step-12"></a>Passaggio 12

Al termine della creazione del gateway applicazione, passare al gateway applicazione nel portale per proseguirne la configurazione.

![Visualizzazione della risorsa del gateway applicazione][10]

Questi passaggi creano un gateway applicazione di base con le impostazioni predefinite per il listener, il pool back-end, le impostazioni HTTP back-end e le regole. Queste impostazioni possono essere modificate in base alla propria distribuzione dopo che è stato completato il provisioning. Come descritto in precedenza, questo processo crea un gateway applicazione predefinito.  I passaggi successivi sono configurare il gateway applicazione aggiungendo membri al pool, modificare le impostazioni e sistemare le regole nel gateway per farlo funzionare correttamente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare probe di integrità personalizzati, vedere [Creare un probe personalizzato per un gateway applicazione con il portale](application-gateway-create-probe-portal.md)

Per informazioni su come configurare l'offload SSL ed evitare costose attività di decrittografia SSL nei server Web, vedere [Configurare un gateway applicazione per l'offload SSL con Azure Resource Manager](application-gateway-ssl-portal.md)

Informazioni su come proteggere i siti Web con [Firewall applicazione Web](application-gateway-webapplicationfirewall-overview.md), una funzionalità del gateway applicazione.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[6]: ./media/application-gateway-create-gateway-portal/figure6.png
[7]: ./media/application-gateway-create-gateway-portal/figure7.png
[8]: ./media/application-gateway-create-gateway-portal/figure8.png
[9]: ./media/application-gateway-create-gateway-portal/figure9.png
[10]: ./media/application-gateway-create-gateway-portal/figure10.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png



<!--HONumber=Oct16_HO2-->


