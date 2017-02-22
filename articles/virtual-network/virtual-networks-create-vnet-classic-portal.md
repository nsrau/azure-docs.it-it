---
title: Creare una rete virtuale Azure (classica) - Portale classico | Documentazione Microsoft
description: Informazioni su come creare una rete virtuale (classica) utilizzando un file di configurazione di rete nel portale di Azure classico.
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 69894a0b-8050-451e-8a25-c513e1bd271e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: d365f7137527d60eb509b4f431295de2218ea706
ms.openlocfilehash: 5ff91cccb711d61ed120e4a4e820d6a5dfc5e4a6


---

# <a name="create-a-virtual-network-classic-with-a-netcfg-file-using-the-azure-classic-portal"></a>Creare una rete virtuale (classica) utilizzando un file netcfg nel portale di Azure classico
[!INCLUDE [virtual-networks-create-vnet-selectors-classic-include](../../includes/virtual-networks-create-vnet-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

In questo articolo viene illustrato come creare una rete virtuale con un file netcfg tramite il modello classico di distribuzione utilizzando il portale di Azure classico. È anche possibile [creare una rete virtuale tramite il modello di distribuzione classico senza utilizzare un file netcfg](virtual-networks-create-vnet-classic-pportal.md) o [creare una rete virtuale tramite il modello di distribuzione Azure Resource Manager ](virtual-networks-create-vnet-arm-pportal.md) tramite il portale di Azure.

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="how-to-create-a-vnet-with-a-network-config-file-in-the-microsoft-azure-classic-portal"></a>Procedura: creare una rete virtuale con un file di configurazione di rete nel portale di Microsoft Azure classico
Azure utilizza un file xml per definire tutte le reti virtuali disponibili per una sottoscrizione. È possibile scaricare questo file e modificarlo per creare reti virtuali tramite il modello di distribuzione classico o per modificare o eliminare le reti virtuali esistenti. In questo articolo viene spiegato come scaricare questo file, definito come un file di configurazione di rete (o netcfg), aggiungervi una rete virtuale e caricarlo per creare la rete virtuale. Per altre informazioni sul file di configurazione di rete, vedere [Schema di configurazione della rete virtuale di Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Per creare una rete virtuale utilizzando un file netcfg tramite il portale di Azure classico, completare i passaggi seguenti:

1. In un browser passare a http://manage.windowsazure.com e, se necessario, accedere con l'account Azure.
2. Scorrere verso il basso l'elenco dei servizi, fare clic su **RETI**, quindi **ESPORTA**, come illustrato nell'immagine seguente:

    ![Reti virtuali di Azure](./media/virtual-networks-create-vnet-classic-portal/networks.png)
3. Nella finestra di dialogo **Esporta configurazione di rete** selezionare la sottoscrizione da cui si desidera esportare la configurazione di rete virtuale, quindi fare clic sul pulsante di segno di spunta nell'angolo inferiore destro della finestra.
4. Seguire le istruzioni del browser per salvare il file **NetworkConfig.xml** . Assicurarsi di annotare il percorso in cui si sta salvando il file.
5. Aprire il file salvato nel passaggio 4 utilizzando una qualsiasi applicazione di editor di testo o XML e cercare l'elemento `<VirtualNetworkSites>` all'interno dell'elemento `<VirtualNetworkConfiguration>`. Se si dispone di reti virtuali esistenti, ciascuna di esse è elencata nel proprio elemento `<VirtualNetworkSite>`. Se il file non contiene un elemento `<VirtualNetworkSites>` all'interno dell'elemento `<VirtualNetworkConfiguration>`, crearne uno.
6. Se il file NetworkConfig esistente non contiene reti virtuali, dopo aver aggiunto la rete virtuale descritta in questo scenario al file Networkconfig.xml, questo sarà simile all'esempio seguente:

    ```xml
    <NetworkConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="TestVNet" Location="Central US">
            <AddressSpace>
              <AddressPrefix>192.168.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>192.168.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>192.168.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    ```
7. Salvare il file di configurazione di rete.
8. Nel portale di Azure classico, fare clic su **NUOVO**, **SERVIZI DI RETE**, **RETE VIRTUALE** e fare clic su **IMPORTA CONFIGURAZIONE** come illustrato nell'immagine seguente:

    ![IMPORTA CONFIGURAZIONE](./media/virtual-networks-create-vnet-classic-portal/import.png)
10. Nella finestra di dialogo **Importa file di configurazione di rete** fare clic su **CERCA FILE**, quindi passare alla cartella in cui il file è stato salvato nel passaggio 7, selezionarlo e quindi fare clic su **Apri** come illustrato nell'immagine seguente:

    ![Pagina Importa file di configurazione di rete ](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure4.png)

    Nell'angolo inferiore destro della finestra fare clic sul pulsante freccia per procedere al passaggio successivo.

9. Nella finestra di dialogo **Compilazione della rete** notare la voce relativa alla nuova rete virtuale, come illustrato nell'immagine seguente:

    ![Pagina Compilazione della rete](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure5.png)
10. Per creare la rete virtuale, fare clic sul pulsante di segno di spunta nell'angolo in basso a destra della finestra di dialogo come illustrato nell'immagine precedente. Dopo alcuni secondi, la rete virtuale verrà visualizzata nell'elenco di reti virtuali disponibili, come illustrato nell'immagine seguente:

    ![Nuova rete virtuale](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure6.png)



<!--HONumber=Jan17_HO5-->


