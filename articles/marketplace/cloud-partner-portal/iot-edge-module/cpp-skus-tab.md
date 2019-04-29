---
title: SKU di moduli Azure IoT Edge | Microsoft Docs
description: Creare SKU per un modulo di IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 370d8160661c1f73124151a3a49d0bb3170dfb77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60910932"
---
# <a name="iot-edge-module-skus-tab"></a>Scheda SKU di moduli per IoT Edge

La scheda **SKU** della pagina **Nuova offerta** consente di creare uno o più SKU e di associarli alla nuova offerta.  È possibile usare vari SKU per differenziare una soluzione per set di funzionalità, modelli di fatturazione o altre caratteristiche.

## <a name="sku-settings"></a>Impostazioni dello SKU

In fase di creazione, una nuova offerta non ha alcuno SKU associato. Per creare un nuovo SKU, seguire questa procedura:

- Nella pagina **Moduli IoT Edge > Nuova offerta** selezionare la scheda **SKU**.
- In SKU selezionare **+ New SKU** (Nuovo SKU) per aprire una finestra di dialogo.

  ![Pulsante New SKU (Nuovo SKU) nella scheda Nuova offerta per i moduli IoT Edge](./media/iot-edge-module-skus-tab-new-sku.png)

- Nella finestra di dialogo **New SKU** (Nuovo SKU) immettere un identificatore per lo SKU e quindi selezionare **OK**.
La tabella seguente indica le convenzioni di denominazione degli identificatori.

La scheda **SKU** viene aggiornata e visualizza i campi modificati per configurare lo SKU. Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio.

|  **Campo**       |     **Descrizione**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU ID** (ID SKU)       | Identificatore dello SKU. Questo nome non può superare i 50 caratteri, costituiti da caratteri alfanumerici minuscoli o trattini (-), ma non può terminare con un trattino. **Nota:** Dopo aver pubblicato l'offerta di, è possibile modificare questo nome. Il nome è visibile pubblicamente negli URL di prodotto. |

## <a name="sku-details"></a>Dettagli dello SKU

Configurare i **dettagli dello SKU** per definire come verrà visualizzato nei siti Web di Azure Marketplace e del portale di Azure.

![Metadati dello SKU per il modulo IoT Edge](media/iot-edge-module-skus-tab-metadata.png)

La tabella seguente descrive lo scopo, il contenuto e la formattazione dei campi in **SKU Details** (Dettagli SKU).

|  **Campo**       |     **Descrizione**                                                          |
|  ---------       |     ---------------                                                          |
| **Titolo**        | Titolo di questo SKU. La lunghezza massima consentita è di 50 caratteri. <br/> Verrà visualizzato nel portale di Azure e usato come nome di modulo predefinito (senza spazi e caratteri speciali) durante la distribuzione. Vedere le immagini seguenti per visualizzare esattamente dove viene visualizzato questo campo.|
| **Summary**      | Breve riepilogo dello SKU. La lunghezza massima consentita è di 100 caratteri. **NON** inserire un riepilogo dell'offerta, ma solo dello SKU.  Questo riepilogo verrà visualizzato in Azure Marketplace. Vedere le immagini seguenti per visualizzare esattamente dove viene visualizzato questo campo.|
| **Descrizione**  | Breve descrizione dello SKU. La lunghezza massima consentita è di 3000 caratteri. NON descrivere l'offerta, ma solo lo SKU. Verrà visualizzata in Azure Marketplace e nel portale di Azure. Nel portale di Azure verrà accodata alla descrizione del marketplace relativa all'offerta definita nella scheda Marketplace.  Può essere uguale al riepilogo dello SKU. Vedere le immagini seguenti per visualizzare esattamente dove viene visualizzato questo campo.|
| **Hide this SKU** (Nascondi questo SKU) | Mantenere l'impostazione predefinita **No**. |

### <a name="sku-example"></a>Esempio di SKU

 Gli esempi seguenti illustrano in che modo vengono visualizzati i campi **Titolo**, **Riepilogo** e **Descrizione** di uno SKU in visualizzazioni diverse.
 
#### <a name="on-the-azure-marketplace-website"></a>Nel sito Web di Azure Marketplace:

- Quando si osservano i dettagli dello SKU:

    ![Modalità di visualizzazione dello SKU nel sito Web di Azure Marketplace](media/iot-edge-module-ampdotcom-pdp-plans.png)

#### <a name="on-the-azure-portal-website"></a>Nel sito Web del portale di Azure:

- Quando si esplorano gli SKU:

    ![Modalità visualizzazione del modulo IoT Edge durante l'esplorazione del portale di Azure n. 1](media/iot-edge-module-portal-browse.png)

    ![Modalità visualizzazione del modulo IoT Edge durante l'esplorazione del portale di Azure n. 2](media/iot-edge-module-portal-product-picker.png)

- Quando si cercano gli SKU:

    ![Modalità visualizzazione del modulo IoT Edge durante le ricerche nel portale di Azure](media/iot-edge-module-portal-search.png)

- Quando si osservano i dettagli dello SKU:

    ![Modalità di visualizzazione del modulo IoT Edge durante l'osservazione dei dettagli del prodotto nel portale](./media/iot-edge-module-portal-pdp.png)

- Quando si distribuisce il modulo:
    
    ![Modalità di visualizzazione del modulo IoT Edge durante la distribuzione](./media/iot-edge-module-deployment.png)

## <a name="sku-content"></a>Contenuto dello SKU

In **Edge Module Images** (Immagini modulo Edge) specificare le informazioni necessarie per caricare il modulo IoT Edge.

Specificare l'accesso al [Registro Azure Container](https://azure.microsoft.com/services/container-registry/) contenente l'immagine del modulo IoT Edge per consentirne il caricamento e la certificazione. Dopo la pubblicazione, il modulo IoT Edge verrà copiato e distribuito mediante un registro contenitori pubblico ospitato da Azure Marketplace.

È possibile specificare come destinazione più piattaforme e indicare varie versioni tramite tag. Altre informazioni sui [tag e sul controllo delle versioni sono disponibili in "Preparare gli asset tecnici del modulo IoT Edge"](./cpp-create-technical-assets.md).

![Immagini del modulo IoT Edge](./media/iot-edge-module-skus-tab-acr.png)

La tabella seguente descrive lo scopo, il contenuto e la formattazione dei campi per:

- **Image Repository Details** (Dettagli repository immagini)
- **Versione immagine**

|  **Campo**       |     **Descrizione**                                                          |
|  ---------       |     ---------------                                                          |
|  ***Image Repository Details*** (Dettagli repository immagini)   |  |
| **ID sottoscrizione**        | ID sottoscrizione di Azure di Registro Azure Container.|
| **Nome del gruppo di risorse**      | Nome del gruppo di risorse di Registro Azure Container.|
| **Nome registro**  | Nome del Registro di sistema di Registro Azure Container. Copiare solo il nome del Registro di sistema, NON il nome del server di accesso (ad esempio, senza `azurecr.io`). |
| **Nome repository**  | Nome del repository di Registro Azure Container contenente il modulo IoT Edge. **Nota:** Dopo aver impostato il nome, non può essere modificato in un secondo momento. Usare un nome univoco in modo che nessun'altra offerta dell'account abbia lo stesso nome. |
| **Nome utente** | Nome utente associato al Registro Azure Container (nome utente amministratore). |
| **Password** | Password associata al Registro Azure Container. |
|  ***Versione immagine***   |  |
| **Image Tag or Digest** (Digest o tag immagine) | Deve includere almeno un tag `latest` e un tag di versione (ad esempio, deve iniziare con `xx.xx.xx-`, dove xx rappresenta un numero). Devono essere [tag di manifesto](https://github.com/estesp/manifest-tool) per specificare come destinazione più piattaforme. È necessario aggiungere anche tutti i tag referenziati da un tag di manifesto per poterli caricare. È possibile aggiungere varie versioni di un modulo IoT Edge mediante tag. Tutti i tag di manifesto (tranne `latest`) devono iniziare con `X.Y-` o `X.Y.Z-`, dove X, Y, Z sono numeri interi. Altre informazioni sui [tag e sul controllo delle versioni sono disponibili in "Preparare gli asset tecnici del modulo IoT Edge"](./cpp-create-technical-assets.md). <br/> Se ad esempio un tag `latest` punta a `1.0.1-linux-x64`, `1.0.1-linux-arm32` e `1.0.1-windows-arm32`, questi 6 tag devono essere aggiunti qui. |

### <a name="help-your-customers-launch-your-iot-edge-module-by-using-default-settings"></a>Aiutare i clienti ad avviare il modulo IoT Edge mediante le impostazioni predefinite

Definire le impostazioni più comuni per distribuire il modulo IoT Edge. Ottimizzare le distribuzioni dei clienti permettendo loro di avviare il modulo IoT Edge con queste impostazioni predefinite.

![Impostazioni predefinite del modulo IoT Edge in fase di distribuzione](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

La tabella seguente descrive lo scopo, il contenuto e la formattazione dei campi per **Default Routes** (Route predefinite), **Default twin desired properties** (Proprietà desiderate del dispositivo gemello predefinite), **Default environment variables** (Variabili di ambiente predefinite) e **Default CreateOptions** (createOptions predefinito).

|  **Campo**       |     **Descrizione**                                                          |
|  ---------       |     ---------------                                                          |
| **Default routes** (Route predefinite)        | Ogni nome e valore delle route predefinite non deve superare i 512 caratteri. È possibile definire fino a 5 route predefinite. Assicurarsi di usare una [sintassi di route](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) corretta nel valore di route. Per fare riferimento al modulo, usarne il nome predefinito, che sarà il **titolo dello SKU** senza spazi e caratteri speciali. Per fare riferimento ad altri moduli non ancora noti, usare la convenzione `<FROM_MODULE_NAME>` per indicare ai clienti che devono aggiornare queste informazioni. Altre informazioni sulle [route IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Ad esempio, se il modulo `ContosoModule` è in ascolto di dati di input in `ContosoInput` e di output in `ContosoOutput`, è opportuno definire le 2 route predefinite seguenti:<br/>- Nome 1: `ToContosoModule`<br/>- Valore 1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- Nome 2: `FromContosoModuleToCloud`<br/>- Valore 2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Default twin desired properties** (Proprietà desiderate del dispositivo gemello predefinite)      | Ogni nome e valore delle proprietà desiderate del dispositivo gemello predefinite non deve superare i 512 caratteri. È possibile definire fino a 5 proprietà nome/valore desiderate del dispositivo gemello. I valori delle proprietà desiderate del dispositivo gemello devono essere un JSON valido senza escape, senza matrici e con una gerarchia annidata massima pari a 4. Altre informazioni sulle [proprietà desiderate del dispositivo gemello](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Se ad esempio un modulo supporta una frequenza di aggiornamento configurabile in modo dinamico tramite le proprietà desiderate del dispositivo gemello, è opportuno definire le proprietà desiderate del dispositivo gemello predefinite seguenti:<br/> - Nome 1: `RefreshRate`<br/>- Valore 1: `60`|
| Default **environment variables** (Variabili di ambiente predefinite)  | Ogni nome e valore delle variabili di ambiente predefinite non deve superare i 512 caratteri. È possibile definire fino a 5 variabili di ambiente nome/valore. <br/>Se ad esempio un modulo richiede di accettare le condizioni d'uso prima dell'avvio, è possibile definire la variabile di ambiente seguente:<br/> - Nome 1: `ACCEPT_EULA`<br/>- Valore 1: `Y`|
| **Default createOptions** (createOptions predefinito)  | createOptions non deve superare i 512 caratteri. Deve essere un JSON valido, senza escape. Altre informazioni su [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Se ad esempio un modulo richiede di associare una porta, è possibile definire il modulo createOptions seguente:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|

<br/> Per salvare le impostazioni dello SKU, fare clic su **Salva**. 

## <a name="next-steps"></a>Passaggi successivi

Usare la [scheda Marketplace](./cpp-marketplace-tab.md) per creare una descrizione del marketplace per l'offerta.
