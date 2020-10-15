---
title: Creazione di zone DNS figlio di Azure
titleSuffix: Azure DNS
description: Esercitazione su come creare zone DNS figlio nel portale di Azure.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: 3f35d39634470ccacffa4d35c272a82725e9001c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89081016"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>Esercitazione: Creazione di una nuova zona DNS figlio

In questa esercitazione verranno illustrate le procedure per: 

> [!div class="checklist"]
> * Accedere al portale di Azure.
> * Creare la zona DNS figlio creando una nuova zona DNS.
> * Creare la zona DNS figlio tramite la zona DNS padre.
> * Verificare la delega NS per la nuova zona DNS figlio.



## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva.  Se non si ha un account, è possibile [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zona DNS di Azure padre esistente.  

Ai fini di questa esercitazione, si userà contoso.com come zona padre e sottodominio.contoso.com come nome di dominio figlio.  Sostituire *contoso.com* con il nome del dominio padre e *sottodominio* con il dominio figlio.  Se la zona DNS padre non è stata creata, vedere la procedura per [creare una zona DNS con il portale di Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal#create-a-dns-zone). 


## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/) con il proprio account Azure.
Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare.

Esistono due modi per creare la zona DNS figlio.
1.  Tramite la pagina del portale "Crea zona DNS".
1.  Tramite la pagina di configurazione della zona DNS padre.


## <a name="create-child-dns-zone-via-create-dns-zone"></a>Creare una zona DNS figlio tramite Crea zona DNS

In questo passaggio si creerà una nuova zona DNS figlio denominata **sottodominio.contoso.com** e la si delegherà alla zona DNS padre esistente **contoso.com**. La zona DNS verrà creata usando le schede della pagina **Crea zona DNS**.
1.  Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**. Verrà visualizzata la finestra **Nuovo**.
1.  Selezionare **Rete**, selezionare **Zona DNS** e quindi selezionare il pulsante **Aggiungi**.

1.  Nella scheda **Informazioni di base** digitare o selezionare i valori seguenti:
    * **Sottoscrizione** Selezionare una sottoscrizione in cui creare la zona.
    * **Gruppo di risorse**: Immettere il gruppo di risorse esistente oppure crearne uno nuovo selezionando **Crea nuovo**, immettere *MyResourceGroup* e selezionare **OK**. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione di Azure.
    * Selezionare questa casella di controllo: **Questa zona è un elemento figlio di una zona esistente già ospitata in DNS di Azure**
    * **Sottoscrizione zona padre**: in questo elenco a discesa cercare e/o selezionare il nome della sottoscrizione in cui è stata creata la zona DNS padre *contoso.com*.
    * **Zona padre**: nella barra di ricerca digitare *contoso.com* per caricarla nell'elenco a discesa. Al termine del caricamento, selezionare *contoso.com* nell'elenco a discesa.
    * **Nome:** Digitare *sottodominio* per l'esempio dell'esercitazione. Si noti che il nome della zona DNS padre *contoso.com* viene automaticamente aggiunto come suffisso al nome quando si seleziona la zona padre nel passaggio precedente.

1. Selezionare **Avanti: Rivedi e crea**.
1. Nella scheda **Rivedi e crea** controllare il riepilogo, correggere eventuali errori di convalida e quindi selezionare **Crea**.
La creazione della zona può richiedere alcuni minuti.

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="Screenshot della pagina Crea zona DNS." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>Creare una zona DNS figlio tramite la pagina di panoramica della zona DNS padre
È anche possibile creare una nuova zona DNS figlio e delegarla nella zona DNS padre usando il pulsante **Zona figlio** nella pagina di panoramica della zona padre. Se si usa questo pulsante, i parametri della zona padre per la zona figlio vengono precompilati automaticamente. 

1.  Nel portale di Azure in **Tutte le risorse** aprire la zona DNS *contoso.com* nel gruppo di risorse **MyResourceGroup**. È possibile immettere *contoso.com* nella casella **Filtra per nome** per trovarla più facilmente.
1.  Nella pagina di panoramica della zona DNS selezionare il pulsante **+ Zona figlio**.

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="Screenshot della pagina Crea zona DNS." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  Verrà visualizzata la pagina Crea zona DNS. L'opzione Zona figlio è già selezionata e la sottoscrizione della zona padre e la zona padre sono già compilate in questa pagina.
1.  Digitare il nome *figlio* per l'esempio dell'esercitazione. Si noti che il nome della zona DNS padre contoso.com viene aggiunto automaticamente come prefisso al nome.
1.  Selezionare **Avanti: Tag** e quindi **Passaggio successivo: Rivedi e crea**.
1.  Nella scheda **Rivedi e crea** controllare il riepilogo, correggere eventuali errori di convalida e quindi selezionare **Crea**.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="Screenshot della pagina Crea zona DNS." border="true"  lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::
## <a name="verify-child-dns-zone"></a>Verificare la zona DNS figlio
Ora che è stata creata una nuova zona DNS figlio *sottodominio.contoso.com*, per verificare che la delega sia stata eseguita correttamente, è necessario controllare che i record del server dei nomi (NS) per la zona figlio siano inclusi nella zona padre, come descritto di seguito.  

**Recuperare i server dei nomi della zona DNS figlio:**

1.  Nel portale di Azure in **Tutte le risorse** aprire la zona DNS *sottodominio.contoso.com* nel gruppo di risorse **MyResourceGroup**. È possibile immettere *sottodominio.contoso.com* nella casella **Filtra per nome** per trovarla più facilmente.
1.  Recuperare i server dei nomi dalla pagina di panoramica della zona DNS. In questo esempio, alla zona contoso.net sono stati assegnati i server dei nomi *ns1-08.azure-dns.com, ns2-08.azure-dns.net, ns3-08.azure-dns.org* e *ns4-08.azure-dns.info*:

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="Screenshot della pagina Crea zona DNS." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**Verificare il record NS nella zona DNS padre:**

In questo passaggio si passa ora alla zona DNS padre *contoso.com* e si verifica che sia stata creata la relativa voce del set di record NS per i server dei nomi delle zone figlio.

1. Nel portale di Azure in **Tutte le risorse** aprire la zona DNS contoso.com nel gruppo di risorse **MyResourceGroup**. È possibile immettere contoso.com nella casella **Filtra per nome** per trovarla più facilmente.
1.  Nella pagina di panoramica delle zone DNS *contoso.com* verificare la presenza dei set di record.
1.  Si noterà che il set di record di tipo NS e il nome sottodominio sono già stati creati nella zona DNS padre. Verificare che i valori per questo set di record siano simili all'elenco dei server dei nomi recuperato dalla zona DNS figlio nel passaggio precedente.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="Screenshot della pagina Crea zona DNS." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>Pulire le risorse
Quando le risorse create in questa esercitazione non sono più necessarie, rimuoverle eliminando il gruppo di risorse **MyResourceGroup**. Aprire il gruppo di risorse **MyResourceGroup** e selezionare **Elimina gruppo di risorse**.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scenari di Zone private di DNS di Azure](private-dns-scenarios.md)
