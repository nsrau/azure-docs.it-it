---
title: Usare applicazione Azure gateway per proteggere le app Web in una soluzione VMware di Azure
description: Configurare applicazione Azure gateway per esporre in modo sicuro le app Web in esecuzione in una soluzione VMware di Azure.
ms.topic: how-to
ms.date: 11/13/2020
ms.openlocfilehash: 02e439989c985354dbe06fa3e231d5daf7099d70
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628993"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Usare applicazione Azure gateway per proteggere le app Web in una soluzione VMware di Azure

[Applicazione Azure gateway](https://azure.microsoft.com/services/application-gateway/) è un servizio di bilanciamento del carico del traffico Web di livello 7 che consente di gestire il traffico verso le applicazioni Web. È disponibile nella soluzione VMware di Azure v 1.0 e v 2.0. Entrambe le versioni testate con app Web in esecuzione in una soluzione VMware di Azure.

Le funzionalità includono: 
- Affinità di sessione basata su cookie
- Routing basato su URL
- WAF (Web application firewall)

Per un elenco completo delle funzionalità, vedere [applicazione Azure funzionalità del gateway](../application-gateway/features.md). 

Questo articolo illustra come usare il gateway applicazione davanti a un server farm Web per proteggere un'app Web in esecuzione in una soluzione VMware di Azure. 

## <a name="topology"></a>Topologia
Il diagramma mostra il modo in cui il gateway applicazione viene usato per proteggere le macchine virtuali (VM) IaaS di Azure, i set di scalabilità di macchine virtuali di Azure o i server locali. Il gateway applicazione considera le macchine virtuali della soluzione VMware di Azure come server locali. 

![Diagramma che illustra il modo in cui il gateway applicazione protegge le macchine virtuali (VM) IaaS di Azure, i set di scalabilità di macchine virtuali di Azure o i server locali.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Applicazione Azure gateway è attualmente l'unico metodo supportato per esporre le app Web in esecuzione su macchine virtuali della soluzione VMware di Azure.

Il diagramma mostra lo scenario di test usato per convalidare il gateway applicazione con le applicazioni Web della soluzione VMware di Azure.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Diagramma che illustra lo scenario di test usato per convalidare il gateway applicazione con le applicazioni Web della soluzione VMware di Azure." border="false":::

L'istanza del gateway applicazione viene distribuita nell'hub in una subnet dedicata. Ha un indirizzo IP pubblico di Azure. È consigliabile attivare la protezione DDoS standard per la rete virtuale. Il server Web è ospitato in una soluzione VMware di Azure cloud privato dietro i router NSX T0 e T1. La soluzione VMware di Azure usa [ExpressRoute copertura globale](../expressroute/expressroute-global-reach.md) per abilitare la comunicazione con l'hub e i sistemi locali.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva.
- Un cloud privato della soluzione VMware di Azure distribuito e in esecuzione.

## <a name="deployment-and-configuration"></a>Distribuzione e configurazione

1. Nella portale di Azure cercare il **gateway applicazione** e selezionare **Crea gateway applicazione**.

2. Specificare i dettagli di base, come illustrato nella figura seguente. quindi selezionare **Avanti: front-end>**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Screenshot che mostra la pagina di creazione del gateway applicazione in portale di Azure.":::

3. Scegliere il tipo di indirizzo IP front-end. Per Public, scegliere un indirizzo IP pubblico esistente o crearne uno nuovo. Selezionare **Avanti: backend>**.

    > [!NOTE]
    > Per i front-end privati sono supportati solo gli SKU standard e Web Application Firewall (WAF).

4. Aggiungere un pool back-end delle VM eseguite nell'infrastruttura della soluzione VMware di Azure. Specificare i dettagli dei server Web in esecuzione nel cloud privato della soluzione VMware di Azure e selezionare **Aggiungi**.  Quindi selezionare **Avanti: Configuration>**.

1. Nella scheda **configurazione** selezionare **Aggiungi una regola di routing**.

6. Nella scheda **listener** specificare i dettagli per il listener. Se si seleziona HTTPS, è necessario fornire un certificato, da un file PFX o da un certificato di Azure Key Vault esistente. 

7. Selezionare la scheda **destinazioni backend** e selezionare il pool back-end creato in precedenza. Per il campo **impostazioni http** , selezionare **Aggiungi nuovo**.

8. Configurare i parametri per le impostazioni HTTP. Selezionare **Aggiungi**.

9. Se si desidera configurare regole basate sul percorso, selezionare **Aggiungi più destinazioni per creare una regola basata sul percorso**. 

10. Aggiungere una regola basata sul percorso e selezionare **Aggiungi**. Ripetere per aggiungere altre regole basate sul percorso. 

11. Al termine dell'aggiunta di regole basate sul percorso, selezionare di nuovo **Aggiungi** . quindi selezionare **Next: Tags>**. 

12. Aggiungere i tag e quindi selezionare **Avanti: rivedere + crea>**.

13. Una convalida viene eseguita sul gateway applicazione. Se l'operazione ha esito positivo, selezionare **Crea** per distribuire.

## <a name="configuration-examples"></a>Esempi di configurazione

Questa sezione illustra come configurare il gateway applicazione con le VM della soluzione VMware di Azure come pool back-end per questi casi d'uso: 

- [Hosting di più siti](#hosting-multiple-sites)
- [Routing per URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hosting di più siti

Questa procedura illustra come definire i pool di indirizzi back-end usando le macchine virtuali in esecuzione in un cloud privato della soluzione VMware di Azure in un gateway applicazione esistente. 

>[!NOTE]
>Questa procedura presuppone che siano presenti più domini, quindi verranno usati esempi di www.contoso.com e www.fabrikam.com.


1. Nel cloud privato creare due diversi pool di macchine virtuali. Uno rappresenta contoso e il secondo fabrikam. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool.png" alt-text="Screenshot che mostra il riepilogo dei dettagli di un server Web in VSphere Client.":::

    Per illustrare questa esercitazione è stato usato Windows Server 2016 con il ruolo Internet Information Services (IIS). Una volta installate le VM, eseguire i comandi di PowerShell seguenti per configurare IIS in ognuna delle macchine virtuali. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. In un'istanza del gateway applicazione esistente selezionare **pool back-end** dal menu a sinistra, selezionare  **Aggiungi** e immettere i dettagli dei nuovi pool. Selezionare **Aggiungi** nel riquadro di destra.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png" alt-text="Screenshot della pagina dei pool back-end per l'aggiunta di pool back-end." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png":::

3. Nella sezione **listener** creare un nuovo listener per ciascun sito Web. Immettere i dettagli per ogni listener e selezionare **Aggiungi**.

4. A sinistra selezionare **impostazioni http** e selezionare **Aggiungi** nel riquadro sinistro. Immettere i dettagli per creare una nuova impostazione HTTP e selezionare **Salva**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png" alt-text="Screenshot della pagina delle impostazioni HTTP per creare una nuova impostazione HTTP." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png":::

5. Creare le regole nella sezione **Rules (regole** ) del menu a sinistra. Associare ogni regola al listener corrispondente. Selezionare **Aggiungi**.

6. Configurare il pool back-end e le impostazioni HTTP corrispondenti. Selezionare **Aggiungi**.

7. Testare la connessione. Aprire il browser preferito e passare ai diversi siti Web ospitati nell'ambiente della soluzione VMware di Azure, ad esempio http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-07.png" alt-text="Screenshot della pagina del browser che mostra la corretta verifica della connessione.":::

### <a name="routing-by-url"></a>Routing per URL

Questa procedura illustra come definire i pool di indirizzi back-end usando le macchine virtuali in esecuzione in un cloud privato della soluzione VMware di Azure in un gateway applicazione esistente. Si creano quindi le regole di routing per garantire che il traffico Web raggiunga i server appropriati nei pool.

1. Nel cloud privato creare un pool di macchine virtuali per rappresentare il Web farm. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool.png" alt-text="Screenshot della pagina nel client VMSphere che mostra il riepilogo di un'altra VM.":::

    Per illustrare questa esercitazione, è stato usato Windows Server 2016 con il ruolo IIS installato. Una volta installate le VM, eseguire i comandi di PowerShell seguenti per configurare IIS per ogni esercitazione sulle VM. 

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

2. Aggiungere tre nuovi pool back-end in un'istanza del gateway applicazione esistente. 

   1. Selezionare **Pool back-end** dal menu a sinistra. 
   1. Selezionare **Aggiungi** e immettere i dettagli del primo pool, **Contoso-Web**. 
   1. Aggiungere una macchina virtuale come destinazione. 
   1. Selezionare **Aggiungi**. 
   1. Ripetere questo processo per **Contoso-images** e **Contoso-video** , aggiungendo una macchina virtuale univoca come destinazione. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png" alt-text="Screenshot della pagina dei pool back-end che mostra l'aggiunta di tre nuovi pool back-end." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png":::

3. Nella sezione **listener** creare un nuovo listener di tipo Basic usando la porta 8080.

4. Nel riquadro di spostamento a sinistra selezionare **impostazioni http** e selezionare **Aggiungi** nel riquadro sinistro. Immettere i dettagli per creare una nuova impostazione HTTP e selezionare **Salva**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-04.png" alt-text="Screenshot della pagina Aggiungi impostazione HTTP che mostra la configurazione delle impostazioni HTTP.":::

5. Creare le regole nella sezione **Rules (regole** ) del menu a sinistra. Associare ogni regola al listener creato in precedenza. Configurare quindi il pool back-end principale e le impostazioni HTTP. Selezionare **Aggiungi**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-07.png" alt-text="Screenshot della pagina Aggiungi una regola di routing per configurare le regole di routing a una destinazione back-end.":::

6. Testare la configurazione. Accedere al gateway applicazione nella portale di Azure e copiare l'indirizzo IP pubblico nella sezione **Panoramica** . 

   1. Aprire una nuova finestra del browser e immettere l'URL `http://<app-gw-ip-address>:8080` . 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-08.png" alt-text="Screenshot della pagina del browser che mostra il test della configurazione completata.":::

   1. Modificare l'URL in `http://<app-gw-ip-address>:8080/images/test.htm`.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-09.png" alt-text="Screenshot di un altro test con esito positivo con il nuovo URL.":::

   1. Modificare nuovamente l'URL in `http://<app-gw-ip-address>:8080/video/test.htm` .

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-10.png" alt-text="Screenshot del test completato con l'URL finale.":::

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di configurazione, vedere la [documentazione del Gateway applicazione Azure](../application-gateway/index.yml) .