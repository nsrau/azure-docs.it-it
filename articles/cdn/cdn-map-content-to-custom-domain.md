---
title: Esercitazione - Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure | Microsoft Docs
description: In questa esercitazione si esegue il mapping del contenuto dell'endpoint della rete CDN di Azure a un dominio personalizzato.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 81db1a7dc01b3d60ee6384f2026ed5ce692ff140
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666101"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Esercitazione: Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure
Questa esercitazione illustra come aggiungere un dominio personalizzato a un endpoint della rete CDN di Azure. Quando si usa un endpoint della rete CDN per distribuire il contenuto, è necessario un dominio personalizzato se si vuole che il nome di dominio sia visibile nell'URL della rete CDN. Avere un nome di dominio visibile può essere pratico per i clienti e utile a scopo di personalizzazione. 

Dopo avere creato un endpoint della rete CDN nel profilo, il nome dell'endpoint, corrispondente a un sottodominio di azureedge.net, viene incluso nell'URL per la distribuzione del contenuto CDN per impostazione predefinita (ad esempio, https:\//contoso.azureedge.net/photo.png). Per comodità, la rete CDN di Azure offre la possibilità di associare un dominio personalizzato a un endpoint della rete CDN. Grazie a questa possibilità, si distribuisce il contenuto con un dominio personalizzato nell'URL invece che con un nome di endpoint (ad esempio, https:\//www.contoso.com/photo.png). 

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> - Creare un record DNS CNAME.
> - Associare il dominio personalizzato all'endpoint della rete CDN.
> - Verificare il dominio personalizzato.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di poter completare i passaggi di questa esercitazione, è necessario creare un profilo della rete CDN e almeno un endpoint della rete CDN. Per altre informazioni, vedere [Avvio rapido: Creare un profilo e un endpoint della rete CDN di Azure](cdn-create-new-endpoint.md).

Se non si dispone già di un dominio personalizzato, è prima necessario acquistarne uno con un provider di dominio. Ad esempio, vedere [Acquistare un nome di dominio personalizzato](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).

Se si usa Azure per ospitare i [domini DNS](https://docs.microsoft.com/azure/dns/dns-overview), è necessario delegare il DNS (Domain Name System) del provider di dominio a un DNS di Azure. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). In caso contrario, se si usa un provider di dominio per gestire il dominio DNS, continuare con [Creare un record DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Creare un record DNS CNAME

Prima di poter usare un dominio personalizzato con un endpoint della rete CDN di Azure, è necessario creare un record di nome canonico (CNAME) con il provider del dominio per puntare all'endpoint della rete CDN. Un record CNAME è un tipo di record DNS che esegue il mapping di un nome di dominio di origine a uno di destinazione. Per la rete CDN di Azure il nome di dominio di origine è il nome di dominio personalizzato, mentre il nome di dominio di destinazione è il nome host dell'endpoint della rete CDN. Dopo che la rete CDN di Azure ha verificato il record CNAME creato, il traffico indirizzato al dominio personalizzato di origine (ad esempio, www\.contoso.com) viene instradato al nome host dell'endpoint della rete CDN di destinazione specificato (ad esempio, contoso.azureedge.net). 

Un dominio personalizzato e il relativo sottodominio possono essere associati a un singolo endpoint alla volta. È tuttavia possibile usare più sottodomini dello stesso dominio personalizzato per endpoint di servizio di Azure diversi usando più record CNAME. È anche possibile eseguire il mapping di un dominio personalizzato con sottodomini diversi allo stesso endpoint della rete CDN.

> [!NOTE]
> Se si usa DNS di Azure come provider di dominio, è possibile usare qualsiasi tipo di record alias per i domini personalizzati. In questa procedura dettagliata viene usato il tipo di record CNAME. Se si usano tipi di record A o AAAA, seguire questi stessi passaggi e sostituire CNAME con il tipo di record scelto. Se si usa un record alias per aggiungere un dominio radice come dominio personalizzato e si vuole abilitare SSL, è necessario eseguire la convalida manuale come descritto in [questo articolo](https://docs.microsoft.com/azure/cdn/cdn-custom-ssl?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate#custom-domain-is-not-mapped-to-your-cdn-endpoint). Per altre informazioni, vedere [Riferire il vertice della zona agli endpoint Rete CDN di Azure](https://docs.microsoft.com/azure/dns/dns-alias#point-zone-apex-to-azure-cdn-endpoints).

## <a name="map-the-temporary-cdnverify-subdomain"></a>Eseguire il mapping del sottodominio cdnverify temporaneo

Quando si esegue il mapping di un dominio esistente in fase di produzione, è necessario tenere presenti alcune considerazioni speciali. Mentre si sta registrando il dominio personalizzato nel portale di Azure, può verificarsi un breve tempo di inattività per il dominio. Per evitare l'interruzione del traffico Web, eseguire prima il mapping del dominio personalizzato al nome host dell'endpoint della rete CDN con il sottodominio cdnverify di Azure per creare un mapping CNAME temporaneo. Con questo metodo, gli utenti possono accedere al dominio senza interruzioni durante l'esecuzione del mapping DNS. 

In caso contrario, se si usa per la prima volta il dominio personalizzato in cui non è in esecuzione traffico di produzione, è possibile eseguire il mapping diretto del dominio personalizzato all'endpoint della rete CDN. Continuare con [Eseguire il mapping del dominio personalizzato permanente](#map-the-permanent-custom-domain).

Per creare un record CNAME con il sottodominio cdnverify:

1. Accedere al sito Web del provider del dominio personalizzato.

2. Cercare la pagina per la gestione dei record DNS facendo riferimento alla documentazione del provider oppure eseguendo una ricerca delle aree del sito Web con etichetta come **Nome del dominio**, **DNS** o **Gestione dei server dei nomi**. 

3. Creare una voce di record CNAME per il dominio personalizzato e completare i campi come illustrato nella tabella seguente (i nomi dei campi possono variare):

    | Source (Sorgente)                    | Type  | Destination                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Origine: immettere il nome di dominio personalizzato, incluso il sottodominio cdnverify, nel formato seguente: cdnverify.&lt;nome di dominio personalizzato&gt;. Ad esempio, cdnverify.www.contoso.com.

    - Digitare: immettere *CNAME*.

    - Destinazione: immettere il nome host dell'endpoint della rete CDN, incluso il sottodominio cdnverify, nel formato seguente: cdnverify. _&lt;nome endpoint&gt;_ .azureedge.net. Ad esempio, cdnverify.contoso.azureedge.net.

4. Salvare le modifiche.

La procedura per il registrar GoDaddy, ad esempio, è la seguente:

1. Accedere al dominio personalizzato da usare e selezionarlo.

2. Nella sezione Domains (Domini) selezionare **Manage All** (Gestisci tutto), quindi selezionare **DNS** | **Manage Zones** (DNS | Gestisci zone).

3. In **Domain Name** (Nome di dominio) immettere il proprio nome di dominio, quindi selezionare **Search** (Cerca).

4. Nella pagina **DNS Management** (Gestione DNS) selezionare **Add** (Aggiungi), quindi selezionare **CNAME** nell'elenco **Type** (Tipo).

5. Completare i campi seguenti della voce CNAME:

    ![Voce CNAME](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Digitare: lasciare selezionato *CNAME*.

    - Host: immettere il sottodominio del dominio personalizzato da usare, incluso il nome del sottodominio cdnverify. Ad esempio, cdnverify.www.

    - Points to (Punta a): immettere il nome host dell'endpoint della rete CDN, incluso il nome di sottodominio cdnverify. Ad esempio, cdnverify.contoso.azureedge.net. 

    - TTL: lasciare selezionato *1 Hour* (1 ora).

6. Selezionare **Salva**.
 
    La voce CNAME viene aggiunta alla tabella di record DNS.

    ![Tabella dei record DNS](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Associare il dominio personalizzato all'endpoint della rete CDN

Dopo avere registrato il dominio personalizzato, è possibile aggiungerlo all'endpoint della rete CDN. 

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare al profilo della rete CDN contenente l'endpoint di cui si vuole eseguire il mapping a un dominio personalizzato.
    
2. Nella pagina **Profilo CDN** selezionare l'endpoint della rete CDN da associare al dominio personalizzato.

   Viene aperta la pagina **Endpoint**.
    
3. Selezionare **Dominio personalizzato**. 

   ![Pulsante Dominio personalizzato della rete CDN](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   Viene aperta la pagina **Aggiungi dominio personalizzato**.

4. Per **Nome host dell'endpoint**, il nome host dell'endpoint da usare come dominio di destinazione del record CNAME è precompilato e derivato dall'URL dell'endpoint della rete CDN:  *&lt;nome host dell'endpoint&gt;* .azureedge.net. Non può essere modificato.

5. Per **Nome host personalizzato**, immettere il dominio personalizzato, incluso il sottodominio, da usare come dominio di origine del record CNAME. Ad esempio, www\.contoso.com o cdn.contoso.com. Non usare il nome del sottodominio cdnverify.

   ![Finestra di dialogo del dominio personalizzato della rete CDN](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. Selezionare **Aggiungi**.

   Azure verifica l'esistenza del record CNAME per il nome di dominio personalizzato immesso. Se il record CNAME è corretto, il dominio personalizzato verrà convalidato. 

   La propagazione delle impostazioni del nuovo dominio personalizzato a tutti i nodi perimetrali della rete CDN può richiedere tempo: 
    - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Microsoft** viene in genere completata in 10 minuti. 
    - La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
    - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Premium di Azure con tecnologia Verizon** viene in genere completata in 10 minuti.   


## <a name="verify-the-custom-domain"></a>Verificare il dominio personalizzato

Dopo aver completato la registrazione del dominio personalizzato, verificare che il dominio personalizzato faccia riferimento all'endpoint della rete CDN.
 
1. Assicurarsi di disporre di contenuto pubblico memorizzato nella cache nell'endpoint. Ad esempio, se l'endpoint della rete CDN è associato a un account di archiviazione, la rete CDN di Azure memorizzerà il contenuto in un contenitore pubblico della cache. Per testare il dominio personalizzato, verificare che il contenitore sia impostato per poter consentire l'accesso pubblico e che contenga almeno un file.

2. Nel browser passare all'indirizzo del file usando il dominio personalizzato. Se il dominio personalizzato è www.contoso.com, ad esempio, l'URL del file memorizzato nella cache sarà simile al seguente: http:\//www.contoso.com/my-public-container/my-file.jpg. Verificare che il risultato sia lo stesso di quando si accede all'endpoint della rete CDN direttamente in  *&lt;nome host dell'endpoint&gt;* .azureedge.net.


## <a name="map-the-permanent-custom-domain"></a>Eseguire il mapping del dominio personalizzato permanente

Se si è verificato che il mapping del sottodominio cdnverify all'endpoint è stato eseguito correttamente (o se si usa un nuovo dominio personalizzato non in produzione), è possibile eseguire il mapping del dominio personalizzato direttamente al nome host dell'endpoint della rete CDN.

Per creare un record CNAME per il dominio personalizzato:

1. Accedere al sito Web del provider del dominio personalizzato.

2. Cercare la pagina per la gestione dei record DNS facendo riferimento alla documentazione del provider oppure eseguendo una ricerca delle aree del sito Web con etichetta come **Nome del dominio**, **DNS** o **Gestione dei server dei nomi**. 

3. Creare una voce di record CNAME per il dominio personalizzato e completare i campi come illustrato nella tabella seguente (i nomi dei campi possono variare):

    | Source (Sorgente)          | Type  | Destination           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azureedge.net |

   - Origine: immettere il nome di dominio personalizzato (ad esempio, www\.contoso.com).

   - Digitare: immettere *CNAME*.

   - Destinazione: immettere il nome host dell'endpoint della rete CDN. Deve essere nel formato seguente: _&lt;nome endpoint&gt;_ .azureedge.net. Ad esempio, contoso.azureedge.net.

4. Salvare le modifiche.

5. Se si è creato in precedenza un record CNAME del sottodominio cdnverify temporaneo, eliminarlo. 

6. Se si usa questo dominio personalizzato nell'ambiente di produzione per la prima volta, seguire i passaggi illustrati in [Associare il dominio personalizzato all'endpoint della rete CDN](#associate-the-custom-domain-with-your-cdn-endpoint) e [Verificare il dominio personalizzato](#verify-the-custom-domain).

La procedura per il registrar GoDaddy, ad esempio, è la seguente:

1. Accedere al dominio personalizzato da usare e selezionarlo.

2. Nella sezione Domains (Domini) selezionare **Manage All** (Gestisci tutto), quindi selezionare **DNS** | **Manage Zones** (DNS | Gestisci zone).

3. In **Domain Name** (Nome di dominio) immettere il proprio nome di dominio, quindi selezionare **Search** (Cerca).

4. Nella pagina **DNS Management** (Gestione DNS) selezionare **Add** (Aggiungi), quindi selezionare **CNAME** nell'elenco **Type** (Tipo).

5. Completare i campi della voce CNAME:

    ![Voce CNAME](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Digitare: lasciare selezionato *CNAME*.

    - Host: immettere il sottodominio del dominio personalizzato da usare. Ad esempio, www o cdn.

    - Points to (Punta a): immettere il nome host dell'endpoint della rete CDN. Ad esempio, contoso.azureedge.net. 

    - TTL: lasciare selezionato *1 Hour* (1 ora).

6. Selezionare **Salva**.
 
    La voce CNAME viene aggiunta alla tabella di record DNS.

    ![Tabella dei record DNS](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Se si ha un record CNAME cdnverify, selezionare l'icona a forma di matita accanto a esso, quindi selezionare l'icona del cestino.

8. Selezionare **Delete** (Elimina) per eliminare il record CNAME.


## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti è stato aggiunto un dominio personalizzato a un endpoint della rete CDN. Se non si vuole più associare l'endpoint a un dominio personalizzato, è possibile rimuovere il dominio personalizzato eseguendo questi passaggi:
 
1. Nel profilo della rete CDN selezionare l'endpoint con il dominio personalizzato che si vuole rimuovere.

2. Nella pagina **Endpoint**, in Domini personalizzati fare clic con il pulsante destro del mouse sul dominio personalizzato che si vuole rimuovere, quindi scegliere **Elimina** dal menu di scelta rapida.  

   Il dominio personalizzato viene dissociato dell'endpoint.


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> - Creare un record DNS CNAME.
> - Associare il dominio personalizzato all'endpoint della rete CDN.
> - Verificare il dominio personalizzato.

Passare alla prossima esercitazione per imparare a configurare HTTPS in un dominio personalizzato della rete CDN di Azure.

> [!div class="nextstepaction"]
> [Esercitazione: Configurare HTTPS in un dominio personalizzato della rete CDN di Azure](cdn-custom-ssl.md)


