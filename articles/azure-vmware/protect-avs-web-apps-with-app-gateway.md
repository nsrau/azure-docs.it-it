---
title: Usare applicazione Azure gateway per proteggere le app Web in una soluzione VMware di Azure
description: Configurare applicazione Azure gateway per esporre in modo sicuro le app Web in esecuzione in una soluzione VMware di Azure (AVS).
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: dfe55ab6b32e9c7b73b8501a16fa6cfaad5bbabe
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87514355"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Usare applicazione Azure gateway per proteggere le app Web in una soluzione VMware di Azure

[Applicazione Azure gateway](https://azure.microsoft.com/services/application-gateway/) è un servizio di bilanciamento del carico del traffico Web di livello 7 che consente di gestire il traffico per le applicazioni Web. Offre molte funzionalità: l'affinità di sessione basata su cookie, il routing basato su URL e il Web Application Firewall (WAF) per citarne alcuni. Per un elenco completo delle funzionalità, vedere [applicazione Azure funzionalità del gateway](../application-gateway/features.md). È disponibile in due versioni, V1 e V2. Entrambi sono stati testati con app Web in esecuzione in una soluzione VMware di Azure (AVS).

Questo articolo illustra in dettaglio uno scenario comune con il gateway applicazione davanti a un server farm Web con un set di configurazioni e consigli per proteggere un'app Web in esecuzione in una soluzione VMware di Azure (AVS). 

## <a name="topology"></a>Topologia
Come illustrato nella figura seguente, il gateway applicazione può essere usato per proteggere le macchine virtuali IaaS di Azure, i set di scalabilità di macchine virtuali di Azure o i server locali. Le macchine virtuali AVS verranno considerate come server locali dal gateway applicazione.

![Il gateway applicazione protegge le VM AVS.](media/protect-avs-web-apps-with-app-gw/app-gateway-protects.png)

> [!IMPORTANT]
> Applicazione Azure gateway è attualmente l'unico metodo supportato per esporre le app Web in esecuzione su macchine virtuali AVS.

Il diagramma seguente illustra lo scenario di test usato per convalidare il gateway applicazione con le applicazioni Web AVS.

![Integrazione del gateway applicazione con AVS che esegue app Web.](media/protect-avs-web-apps-with-app-gw/app-gateway-avs-scenario.png)

L'istanza del gateway applicazione viene distribuita nell'hub in una subnet dedicata. Ha un indirizzo IP pubblico di Azure; è consigliabile attivare la protezione DDoS standard per la rete virtuale. Il server Web è ospitato in un cloud privato AVS dietro i router NSX T0 e T1. AVS USA [ExpressRoute copertura globale](../expressroute/expressroute-global-reach.md) per abilitare la comunicazione con l'hub e i sistemi locali.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva.
- Un cloud privato AVS distribuito e in esecuzione.

## <a name="deployment-and-configuration"></a>Distribuzione e configurazione

1. Nella portale di Azure cercare il **gateway applicazione** e selezionare **Crea gateway applicazione**.

2. Specificare i dettagli di base, come illustrato nella figura seguente. quindi selezionare **Avanti: front-end>**. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/create-app-gateway.png" alt-text="Creazione del gateway applicazione":::

3. Scegliere il tipo di indirizzo IP front-end. Per Public, scegliere un indirizzo IP pubblico esistente o crearne uno nuovo. Selezionare **Avanti: backend>**.

    > [!NOTE]
    > Per i front-end privati sono supportati solo gli SKU standard e Web Application Firewall (WAF).

4. Aggiungere quindi un pool back-end, che descrive un set di istanze che fanno parte dell'applicazione o del servizio (in questo caso, le macchine virtuali in esecuzione nell'infrastruttura AVS). Specificare i dettagli dei server Web in esecuzione nel cloud privato AVS e selezionare **Aggiungi**; quindi selezionare **Avanti: Configuration>**.

1. Nella scheda **configurazione** selezionare **Aggiungi una regola di routing**.

6. Nella scheda **listener** specificare i dettagli per il listener. Se si seleziona HTTPS, è necessario specificare un certificato, da un file PFX o da un certificato esistente da Azure Key Vault. 

7. Selezionare la scheda **destinazioni backend** e selezionare il pool back-end creato in precedenza. Per il campo **impostazioni http** , selezionare **Aggiungi nuovo**.

8. Configurare i parametri per le impostazioni HTTP. Selezionare **Aggiungi**.

9. Se si desidera configurare regole basate sul percorso, selezionare **Aggiungi più destinazioni per creare una regola basata sul percorso**. 

10. Aggiungere una regola basata sul percorso e selezionare **Aggiungi**. Ripetere per aggiungere altre regole basate sul percorso. 

11. Al termine dell'aggiunta di regole basate sul percorso, selezionare di nuovo **Aggiungi** . quindi selezionare **Next: Tags>**. 

12. Aggiungere i tag desiderati. Selezionare **Avanti: rivedere + crea>**.

13. Una convalida viene eseguita sul gateway applicazione. Se l'operazione ha esito positivo, selezionare **Crea** per distribuire.

## <a name="configuration-examples"></a>Esempi di configurazione

In questa sezione si apprenderà come configurare il gateway applicazione con le macchine virtuali AVS come pool back-end per i casi d'uso seguenti: 

- [Hosting di più siti](#hosting-multiple-sites)
- [Routing per URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hosting di più siti

È possibile usare la portale di Azure per configurare l'hosting di più siti Web quando si crea un gateway applicazione. In questa esercitazione si definiscono i pool di indirizzi back-end usando le macchine virtuali in esecuzione in un cloud privato AVS in un gateway applicazione esistente. Il gateway applicazione fa parte di una rete virtuale Hub, come descritto in [integrare AVS in un'architettura hub-spoke](concepts-avs-hub-and-spoke-integration.md). In questa esercitazione si presuppone che l'utente sia proprietario di più domini e che utilizzi esempi di www.contoso.com e www.fabrikam.com.

1. Creare le macchine virtuali. Nel cloud privato AVS creare due diversi pool di macchine virtuali. uno rappresenterà Contoso e il secondo fabrikam. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs.png" alt-text="Pool di server Web in AVS":::

    Per illustrare questa esercitazione, è stato usato Windows Server 2016 con il ruolo Internet Information Services (IIS) installato. Una volta installate le macchine virtuali, eseguire i comandi di PowerShell seguenti per configurare IIS in ognuna delle macchine virtuali. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Aggiungere i pool back-end. In un'istanza del gateway applicazione esistente selezionare **pool back-end** dal menu a sinistra, selezionare **Aggiungi**e immettere i dettagli dei nuovi pool. Selezionare **Aggiungi** nel riquadro di destra.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png" alt-text="Configurazione del pool back-end" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png":::

3. Nella sezione **listener** creare un nuovo listener per ciascun sito Web. Immettere i dettagli per ogni listener e selezionare **Aggiungi**.

4. Nel riquadro di spostamento a sinistra selezionare **impostazioni http** e selezionare **Aggiungi** nel riquadro sinistro. Immettere i dettagli per creare una nuova impostazione HTTP e selezionare **Salva**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png" alt-text="Configurazione impostazioni HTP" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png":::

5. Creare le regole nella sezione **Rules (regole** ) del menu a sinistra. Associare ogni regola al listener corrispondente. Selezionare **Aggiungi**.

6. Configurare il pool back-end e le impostazioni HTTP corrispondenti. Selezionare **Aggiungi**.

7. Testare la connessione. Aprire il browser preferito e accedere ai diversi siti Web ospitati nell'ambiente AVS, ad esempio http://www.fabrikam.com .

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-07.png" alt-text="Configurazione back-end regola":::

### <a name="routing-by-url"></a>Routing per URL

È possibile usare applicazione Azure gateway per configurare le regole di routing basato su percorsi URL. In questa esercitazione si definiscono i pool di indirizzi back-end usando le macchine virtuali in esecuzione in un cloud privato AVS in un gateway applicazione esistente. Il gateway applicazione fa parte di una rete virtuale dell'hub, come descritto in [AVS documentazione di integrazione nativa di Azure](concepts-avs-hub-and-spoke-integration.md). Si creano quindi le regole di routing per garantire che il traffico Web raggiunga i server appropriati nei pool.

1. Creare le macchine virtuali. Nel cloud privato AVS creare un pool di macchine virtuali per rappresentare il Web farm. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs.png" alt-text="Pool di server Web in AVS":::

    Per illustrare questa esercitazione, è stato usato Windows Server 2016 con il ruolo IIS installato. Una volta installate le macchine virtuali, eseguire i comandi di PowerShell seguenti per configurare IIS per l'esercitazione in ogni macchina virtuale. 

    La prima macchina virtuale, Contoso-Web-01, ospiterà il sito Web principale.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    La seconda macchina virtuale, Contoso-Web-02, ospiterà il sito images.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    La terza macchina virtuale, Contoso-Web-03, ospiterà il sito del video.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Aggiungere i pool back-end. Sarà necessario aggiungere tre nuovi pool back-end in un'istanza del gateway applicazione esistente. Selezionare **Pool back-end** dal menu a sinistra. Selezionare **Aggiungi** e immettere i dettagli del primo pool, **Contoso-Web**. Aggiungere una macchina virtuale come destinazione. Selezionare **Aggiungi**. Ripetere questo processo per **Contoso-images** e **Contoso-video**, aggiungendo una macchina virtuale univoca a ciascuna come destinazione. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Creazione di pool back-end" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png":::

3. Nella sezione **listener** creare un nuovo listener di tipo Basic usando la porta 8080.

4. Nel riquadro di spostamento a sinistra selezionare **impostazioni http** e selezionare **Aggiungi** nel riquadro sinistro. Immettere i dettagli per creare una nuova impostazione HTTP e selezionare **Salva**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Configurazione impostazioni HTP":::

5. Creare le regole nella sezione **Rules (regole** ) del menu a sinistra. Associare ogni regola al listener creato in precedenza. Configurare quindi il pool back-end principale e le impostazioni HTTP. Selezionare **Aggiungi**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Configurazione back-end regola":::

6. Testare la configurazione. Accedere al gateway applicazione nella portale di Azure e nella sezione **Panoramica** copiare l'indirizzo IP pubblico. Quindi aprire una nuova finestra del browser e immettere l'URL `http://<app-gw-ip-address>:8080` . 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Test di configurazione":::

    Modificare l'URL in `http://<app-gw-ip-address>:8080/images/test.htm`.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Test di configurazione":::

    Modificare nuovamente l'URL in `http://<app-gw-ip-address>:8080/video/test.htm` .

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Test di configurazione":::

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di configurazione, vedere la [documentazione del Gateway applicazione Azure](https://docs.microsoft.com/azure/application-gateway/) .
