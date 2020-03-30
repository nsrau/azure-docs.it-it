---
title: Spostare l'indirizzo IP pubblico di Azure in un'altra area di Azure usando il portale di AzureMove Azure Public IP to another Azure region using the Azure portal
description: Usare il modello di Azure Resource Manager per spostare l'indirizzo IP pubblico di Azure da un'area di Azure a un'altra usando il portale di Azure.Use Azure Resource Manager template to move Azure Public IP from an Azure region to another using the Azure portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6d10265e8383b68ebe13c95d8b2a9632668e85da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641402"
---
# <a name="move-azure-public-ip-to-another-region-using-the-azure-portal"></a>Spostare l'indirizzo IP pubblico di Azure in un'altra area usando il portale di AzureMove Azure Public IP to another region using the Azure portal

Esistono vari scenari in cui si desidera spostare gli indirizzi IP pubblici di Azure esistenti da un'area a un'altra. Ad esempio, è possibile creare un indirizzo IP pubblico con la stessa configurazione e sku per il test. È anche possibile spostare un indirizzo IP pubblico in un'altra area come parte della pianificazione del ripristino di emergenza.

Gli indirizzi IP pubblici di Azure sono specifici dell'area e non possono essere spostati da un'area a un'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente di un indirizzo IP pubblico.  È quindi possibile creare temporaneamente la risorsa in un'altra area esportando l'indirizzo IP pubblico in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area.  Per altre informazioni su Resource Manager e sui modelli, vedere Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager tramite il portale di Azure.For more information on Resource Manager and templates, see [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che l'indirizzo IP pubblico di Azure si trovi nell'area di Azure da cui si vuole spostare.

- Gli indirizzi IP pubblici di Azure non possono essere spostati tra le aree.  Sarà necessario associare il nuovo IP pubblico alle risorse nell'area di destinazione.

- Per esportare una configurazione IP pubblica e distribuire un modello per creare un indirizzo IP pubblico in un'altra area, è necessario il ruolo Collaboratore di rete o versione successiva.

- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato aservizi di bilanciamento del carico, gruppi di sicurezza di rete (NSG) e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare indirizzi IP pubblici nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di indirizzi IP pubblici per questo processo.  Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparare e spostare
I passaggi seguenti illustrano come preparare l'indirizzo IP pubblico per lo spostamento della configurazione usando un modello di Resource Manager e spostare la configurazione IP pubblica nell'area di destinazione usando il portale di Azure.The following steps show how to prepare the public IP for the configuration move using a Resource Manager template, and move the public IP configuration to the target region using the Azure portal.

### <a name="export-the-template-and-deploy-from-a-script"></a>Esportare il modello e distribuirlo da uno script

1. Accedere a**Gruppi di risorse**del portale > di [Azure](https://portal.azure.com).
2. Individuare il gruppo di risorse che contiene l'indirizzo IP pubblico di origine e fare clic su di esso.
3. Selezionare >**modello di esportazione** **impostazioni** > .
4. Scegliere **Distribuisci** nel pannello **Esporta modello.**
5. Fare clic su **MODELLO** > **Modificare i parametri** per aprire il file **parameters.json** nell'editor online.
8. Per modificare il parametro del nome IP pubblico, modificare il**valore** della proprietà in **parametri** > dal nome IP pubblico di origine al nome dell'IP pubblico di destinazione, assicurarsi che il nome sia racchiuso tra virgolette:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  Fare clic su **Salva** nell'editor.

9.  Fare clic su **MODELLO** > **Modifica modello** per aprire il file **template.json** nell'editor online.

10. Per modificare l'area di destinazione in cui verrà spostato l'IP pubblico, modificare la proprietà **location** in **resources**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. Per ottenere i codici di ubicazione, vedere [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  Il codice per una regione è il nome dell'area senza spazi, **Central US** = **centralus**.

12. È inoltre possibile modificare altri parametri nel modello, se lo si desidera, e sono facoltativi a seconda dei requisiti:

    * **Sku** - È possibile modificare lo sku dell'IP pubblico nella configurazione da standard a basic o basic to standard modificando la proprietà **sku** > **name** nel file **template.json:**

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Per ulteriori informazioni sulle differenze tra ips pubblici sku di base e standard, vedere [Creare, modificare o eliminare un indirizzo IP pubblico:](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)

    * Metodo di **allocazione IP pubblico** e **Timeout inattivo:** è possibile modificare entrambe queste opzioni nel modello modificando la proprietà **publicIPAllocationMethod** da **Dynamic** a **Static** a **Dynamic**. **Static** Il timeout di inattività può essere modificato modificando la proprietà **idleTimeoutInMinutes** impostandola sulla quantità desiderata.  Il valore predefinito è **4**:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Per ulteriori informazioni sui metodi di allocazione e sui valori di timeout di inattività, vedere [Creare, modificare o eliminare un indirizzo IP pubblico.](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)


13. Fare clic su **Salva** nell'editor online.

14. Fare clic su**Sottoscrizione** **BASICS** > per scegliere la sottoscrizione in cui verrà distribuito l'indirizzo IP pubblico di destinazione.

15. Fare clic su**Gruppo di risorse** **BASICS** > per scegliere il gruppo di risorse in cui verrà distribuito l'indirizzo IP pubblico di destinazione.  È possibile fare clic su **Crea nuovo** per creare un nuovo gruppo di risorse per l'indirizzo IP pubblico di destinazione.  Verificare che il nome non sia uguale al gruppo di risorse di origine dell'indirizzo IP pubblico di origine esistente.

16. Verificare**che** **Percorso BASICS** > sia impostato sul percorso di destinazione in cui si desidera distribuire l'indirizzo IP pubblico.

17. Verificare in **CELLE** che il nome corrisponda al nome immesso nell'editor dei parametri precedente.

18. Selezionare la casella in **TERMINI E CONDIZIONI**.

19. Fare clic sul pulsante **Acquista** per distribuire l'indirizzo IP pubblico di destinazione.

## <a name="discard"></a>Discard

Se si desidera eliminare l'indirizzo IP pubblico di destinazione, eliminare il gruppo di risorse che contiene l'ip pubblico di destinazione.  A tale scopo, selezionare il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore della pagina di panoramica.

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento dell'indirizzo IP pubblico, eliminare l'indirizzo IP pubblico di origine o il gruppo di risorse. A tale scopo, selezionare l'indirizzo IP pubblico o il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore di ogni pagina.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un indirizzo IP pubblico di Azure da un'area a un'altra e sono stati puliti le risorse di origine.  Per altre informazioni sullo spostamento delle risorse tra aree e il ripristino di emergenza in Azure, vedere:To learn more about moving resources between regions and disaster recovery in Azure, refer to:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
