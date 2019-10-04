---
title: Spostare il gruppo di sicurezza di rete di Azure (NSG) in un'altra area di Azure usando il portale di Azure
description: Usare il modello di Azure Resource Manager per spostare il gruppo di sicurezza di rete di Azure da un'area di Azure a un'altra usando il portale di Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 839e608aa4bba26712ae5b0c160da40db279bbc9
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219195"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Spostare il gruppo di sicurezza di rete di Azure (NSG) in un'altra area usando il portale di Azure

Esistono diversi scenari in cui si vuole spostare il gruppi esistente da un'area a un'altra. Ad esempio, è possibile creare un NSG con le stesse regole di configurazione e sicurezza per il test. Potrebbe anche essere necessario spostare un NSG in un'altra area nell'ambito della pianificazione del ripristino di emergenza.

I gruppi di sicurezza di Azure non possono essere spostati da un'area all'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare le regole di sicurezza e configurazione esistenti di un NSG.  È quindi possibile organizzare la risorsa in un'altra area esportando il NSG in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area.  Per ulteriori informazioni su Gestione risorse e sui modelli, [vedere Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Prerequisiti

- Verificare che il gruppo di sicurezza di rete di Azure si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

- I gruppi di sicurezza di rete di Azure non possono essere spostati tra le aree.  È necessario associare il nuovo NSG alle risorse nell'area di destinazione.

- Per esportare una configurazione NSG e distribuire un modello per creare un NSG in un'altra area, è necessario il ruolo Collaboratore rete o versione successiva.

- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato a bilanciamento del carico, indirizzi IP pubblici e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare gruppi nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di gruppi per questo processo.  Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparare e spostare
I passaggi seguenti illustrano come preparare il gruppo di sicurezza di rete per la configurazione e la regola di sicurezza spostare usando un modello di Gestione risorse e spostare le regole di sicurezza e configurazione NSG nell'area di destinazione usando il portale.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Esportare il modello e distribuirlo dal portale

1. Accedere ai**gruppi di risorse** [portale di Azure](https://portal.azure.com) > .
2. Individuare il gruppo di risorse che contiene il NSG di origine e fare clic su di esso.
3. Selezionare > **Impostazioni** > **Esporta modello**.
4. Scegliere **Distribuisci** nel pannello **Esporta modello** .
5. Fare clic su **modello** > **modifica parametri** per aprire il file **Parameters. JSON** nell'editor online.
6. Per modificare il parametro del nome NSG, modificare la proprietà **value** in **Parameters**:

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

7. Modificare il valore di NSG di origine nell'editor con il nome desiderato per il NSG di destinazione. Assicurarsi di racchiudere il nome tra virgolette.

8.  Fare clic su **Salva** nell'editor.

9.  Fare clic **su modello** **modifica modello** per aprire il file **template. JSON** nell'editor online. > 

10. Per modificare l'area di destinazione in cui verranno spostate le regole di sicurezza e configurazione NSG, modificare la proprietà **location** in **Resources** nell'editor online:

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

11. Per ottenere i codici di posizione dell'area, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Il codice per un'area è il nome dell'area senza spazi, Central **Stati Uniti** = **centrali.**

12. È anche possibile modificare altri parametri nel modello, se si sceglie, e sono facoltativi in base ai requisiti:

    * **Regole di sicurezza** : è possibile modificare le regole distribuite nel NSG di destinazione aggiungendo o rimuovendo regole nella sezione **SecurityRules** del file **template. JSON** :

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

      Per completare l'aggiunta o la rimozione delle regole nel NSG di destinazione, è necessario modificare anche i tipi di regole personalizzati alla fine del file **template. JSON** nel formato dell'esempio seguente:

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

14. Fare clic su**sottoscrizione** di **base** > per scegliere la sottoscrizione in cui verrà distribuita la NSG di destinazione.

15. Fare clic su**gruppo di risorse** **nozioni di base** > per scegliere il gruppo di risorse in cui verrà distribuito il NSG di destinazione.  È possibile fare clic su **Crea nuovo** per creare un nuovo gruppo di risorse per il NSG di destinazione.  Verificare che il nome non sia uguale al gruppo di risorse di origine del NSG esistente.

16. Il**percorso** di **base** > della verifica è impostato sul percorso di destinazione in cui si desidera distribuire il NSG.

17. Verificare in **Impostazioni** che il nome corrisponda al nome immesso nell'editor di parametri precedente.

18. Selezionare la casella in **termini e condizioni**.

19. Fare clic sul pulsante **Acquista** per distribuire il gruppo di sicurezza di rete di destinazione.

## <a name="discard"></a>Annulla

Se si vuole rimuovere il NSG di destinazione, eliminare il gruppo di risorse che contiene il NSG di destinazione.  A tale scopo, selezionare il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore della pagina Overview (panoramica).

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento del NSG, eliminare il NSG di origine o il gruppo di risorse. A tale scopo, selezionare il gruppo di sicurezza di rete o il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore di ogni pagina.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un gruppo di sicurezza di rete di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo trasferimento di risorse tra aree e ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
