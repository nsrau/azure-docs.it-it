---
title: Spostare il gruppo di sicurezza di rete di Azure in un'altra area di Azure usando il portale di AzureMove Azure network security group (NSG) to another Azure region using the Azure portal
description: Usare il modello di Azure Resource Manager per spostare il gruppo di sicurezza di rete di Azure da un'area di Azure a un'altra usando il portale di Azure.Use Azure Resource Manager template to move Azure network security group from an Azure region to another using the Azure portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: dce267178c3caf813ccdcac4bba86ccfde3f3421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647187"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Spostare il gruppo di sicurezza di rete di Azure in un'altra area tramite il portale di AzureMove Azure network security group (NSG) to another region using the Azure portal

Esistono vari scenari in cui si desidera spostare i gruppi di sicurezza di rete esistenti da un'area all'altra. Ad esempio, è possibile creare un gruppo di sicurezza di rete con le stesse regole di configurazione e sicurezza per il test. È anche possibile spostare un gruppo di sicurezza di rete in un'altra area come parte della pianificazione del ripristino di emergenza.

I gruppi di sicurezza di Azure non possono essere spostati da un'area all'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare le regole di configurazione e sicurezza esistenti di un gruppo di sicurezza di rete.  È quindi possibile gestire temporaneamente la risorsa in un'altra area esportando il gruppo di sicurezza di rete in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area.  Per altre informazioni su Resource Manager e sui modelli, vedere Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager tramite il portale di Azure.For more information on Resource Manager and templates, see [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che il gruppo di sicurezza di rete di Azure si trovi nell'area di Azure da cui si vuole spostare.

- I gruppi di sicurezza di rete di Azure non possono essere spostati da un aree all'altro.  È necessario associare il nuovo gruppo di sicurezza di rete alle risorse nell'area di destinazione.

- Per esportare una configurazione del gruppo di sicurezza di rete e distribuire un modello per creare un gruppo di sicurezza di rete in un'altra area, è necessario il ruolo Collaboratore di rete o versione successiva.

- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato a servizi di bilanciamento del carico, INDIRIZZI IP pubblici e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare gruppi di sicurezza di rete nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di gruppi di sicurezza di sicurezza di sicurezza di sicurezza per questo processo.  Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparare e spostare
I passaggi seguenti illustrano come preparare il gruppo di sicurezza di rete per lo spostamento delle regole di sicurezza e configurazione usando un modello di Resource Manager e spostare le regole di configurazione e sicurezza del gruppo di sicurezza di rete nell'area di destinazione tramite il portale.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Esportare il modello e distribuirlo dal portale

1. Accedere a**Gruppi di risorse**del portale > di [Azure](https://portal.azure.com).
2. Individuare il gruppo di risorse che contiene il gruppo di sicurezza di rete di origine e fare clic su di esso.
3. Selezionare >**modello di esportazione** **impostazioni** > .
4. Scegliere **Distribuisci** nel pannello **Esporta modello.**
5. Fare clic su **MODELLO** > **Modificare i parametri** per aprire il file **parameters.json** nell'editor online.
6. Per modificare il parametro del nome del gruppo di sicurezza di rete, modificare la proprietà **value** in **Parameters**:

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Modificare il valore del gruppo di sicurezza di gruppo di origine nell'editor con un nome di propria scelta per il gruppo di sicurezza di gruppo di destinazione. Assicurarsi di racchiudere il nome tra virgolette.

8.  Fare clic su **Salva** nell'editor.

9.  Fare clic su **MODELLO** > **Modifica modello** per aprire il file **template.json** nell'editor online.

10. Per modificare l'area di destinazione in cui verranno spostate le regole di configurazione e sicurezza del gruppo di sicurezza, modificare la proprietà **location** in **risorse** nell'editor online:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. Per ottenere i codici di ubicazione, vedere [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  Il codice per una regione è il nome dell'area senza spazi, **Central US** = **centralus**.

12. È inoltre possibile modificare altri parametri nel modello, se lo si desidera, e sono facoltativi a seconda dei requisiti:

    * Regole di **sicurezza:** è possibile modificare le regole distribuite nel gruppo di sicurezza di rete di destinazione aggiungendo o rimuovendo regole alla sezione securityRules del file **template.json:Security** rules - You can edit which rules are deployed into the target NSG by adding or removing rules to the **securityRules** section in the template.json file:

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      Per completare l'aggiunta o la rimozione delle regole nel gruppo di sicurezza di rete di destinazione, è necessario modificare anche i tipi di regole personalizzate alla fine del file **template.json** nel formato dell'esempio seguente:

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. Fare clic su **Salva** nell'editor online.

14. Fare clic su**Sottoscrizione** **BASICS** > per scegliere la sottoscrizione in cui verrà distribuito il gruppo di sicurezza di rete di destinazione.

15. Fare clic su Gruppo di**risorse** **BASICS** > per scegliere il gruppo di risorse in cui verrà distribuito il gruppo di sicurezza di rete di destinazione.  È possibile fare clic su **Crea nuovo** per creare un nuovo gruppo di risorse per il gruppo di sicurezza di rete di destinazione.  Verificare che il nome non sia uguale al gruppo di risorse di origine del gruppo di sicurezza di rete esistente.

16. Verificare**che** **Percorso BASICS** > sia impostato sul percorso di destinazione in cui si desidera distribuire il gruppo di sicurezza di rete.

17. Verificare in **CELLE** che il nome corrisponda al nome immesso nell'editor dei parametri precedente.

18. Selezionare la casella in **TERMINI E CONDIZIONI**.

19. Fare clic sul pulsante **Acquista** per distribuire il gruppo di sicurezza di rete di destinazione.

## <a name="discard"></a>Discard

Se si desidera eliminare il gruppo di sicurezza di rete di destinazione, eliminare il gruppo di risorse che contiene il gruppo di sicurezza di rete di destinazione.  A tale scopo, selezionare il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore della pagina di panoramica.

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento del gruppo di sicurezza di rete, eliminare il gruppo di sicurezza di rete o il gruppo di risorse di origine. A tale scopo, selezionare il gruppo di sicurezza di rete o il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore di ogni pagina.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un gruppo di sicurezza di rete di Azure da un'area a un'altra e sono stati puliti le risorse di origine.  Per altre informazioni sullo spostamento delle risorse tra aree e il ripristino di emergenza in Azure, vedere:To learn more about moving resources between regions and disaster recovery in Azure, refer to:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
