---
title: Usare Azure Data Studio per gestire l'istanza di PostgreSQL
description: Usare Azure Data Studio per gestire l'istanza di PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: fc0ad45f575f9190f15b61acdf476c716b7f1638
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940782"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Usare Azure Data Studio per gestire il gruppo di server con iperscalabilità PostgreSQL abilitata per Azure Arc


Questo articolo illustra come:
- gestire le istanze di PostgreSQL con visualizzazioni dashboard quali panoramica, stringhe di connessione, proprietà, Integrità risorse...
- usare i dati e lo schema

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Installare azdata, Azure Data Studio e l'interfaccia della riga di comando di Azure](install-client-tools.md)
- Installare in Azure Data Studio l' **interfaccia** della riga di comando di Azure e le estensioni **Azure Arc** e **PostgreSQL**
- Creare il [controller di dati di Azure Arc](create-data-controller-using-azdata.md)
- Avvia Azure Data Studio

## <a name="connect-to-the-azure-arc-data-controller"></a>Connettersi al controller di dati di Azure Arc

In Azure Data Studio espandere il nodo **controller di Azure Arc** e selezionare il pulsante **Connetti controller** :

Immettere le informazioni di connessione al controller di dati di Azure:

- **URL controller:**

    URL per la connessione al controller in Kubernetes. Immesso nel formato `https://<IP_address_of_the_controller>:<Kubernetes_port.` ad esempio:

    ```console
    https://12.345.67.890:30080
    ```
- **Nome utente:**

    Nome dell'account utente utilizzato per la connessione al controller. Usare il nome usato in genere quando si esegue `azdata login` . Non è il nome dell'utente PostgreSQL usato per connettersi al motore di database PostgreSQL in genere da PSQL.
- **Password:** Password dell'account utente usato per la connessione al controller


Azure Data Studio Mostra il controller di dati Arc. Espanderla e visualizzare l'elenco delle istanze di PostgreSQL gestite.

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Gestire i gruppi di server con iperscalabilità di PostgreSQL abilitati per Azure Arc

Fare clic con il pulsante destro del mouse sull'istanza di PostgreSQL che si vuole gestire e selezionare [Gestisci]

Visualizzazione dashboard di PostgreSQL:

Che include diversi dashboard elencati sul lato sinistro del riquadro:

- **Panoramica:** Visualizza le informazioni di riepilogo sull'istanza, ad esempio il nome, l'ID sottoscrizione di Azure, la configurazione, la versione del motore di database, gli endpoint per Grafana e Kibana...
- **Stringhe di connessione:** Visualizza varie stringhe di connessione potrebbe essere necessario connettersi all'istanza di PostgreSQL come PSQL, Node.js, PHP, Ruby...
- **Proprietà:** Visualizza varie proprietà, ad esempio il nome utente dell'amministratore PostgreSQL, il gruppo di risorse associato per la risorsa Shadow...
- **Diagnosticare e risolvere i problemi:** È la pagina di destinazione in cui sono disponibili varie risorse che consentono di risolvere i problemi relativi all'istanza durante l'espansione dei notebook per la risoluzione dei problemi
- **Nuova richiesta di supporto:** È la pagina di destinazione dalla quale sarà possibile richiedere assistenza da parte dei servizi di supporto tecnico che avviano l'annuncio dell'anteprima pubblica.

## <a name="work-with-your-data-and-schema"></a>Usare i dati e lo schema

Sul lato sinistro della finestra di Azure Data Studio espandere i **Server**del nodo:

E selezionare [Aggiungi connessione] e immettere i dettagli della connessione per l'istanza di PostgreSQL:
- **Tipo di connessione:** PostgreSQL
- **Nome server:** immettere il nome dell'istanza di PostgreSQL. Ad esempio: postgres01
- **Tipo di autenticazione:** Password
- **Nome utente:** ad esempio, è possibile usare il nome utente dell'amministratore PostgreSQL standard o predefinito. Si noti che questo campo fa distinzione tra maiuscole e minuscole.
- **Password:** la password del nome utente di PostgreSQL è presente nella stringa di connessione PSQL nell'output del `azdata postgres server endpoint -n postgres01` comando
- **Nome database:** impostare il nome del database a cui si desidera connettersi. È possibile impostare l' __impostazione predefinita__
- **Gruppo di server:** è possibile impostare il __valore predefinito__
- **Nome (facoltativo):** è possibile lasciare vuoto questo campo
- **Avanzate**
    - **Indirizzo IP host:** è l'indirizzo IP pubblico del cluster Kubernetes
    - **Port:** è la porta su cui è in ascolto l'istanza di PostgreSQL. È possibile trovare questa porta alla fine della stringa di connessione PSQL nell'output del `azdata postgres server endpoint -n postgres01` comando. Non la porta 30080 su cui Kubernetes è in ascolto e che è stato immesso durante la connessione al controller di dati di Azure in Azure Data Studio.
    - **Altri parametri:** Devono essere autoespliciti. è possibile vivere con i valori predefiniti/vuoti con cui vengono visualizzati.

Selezionare **[OK] e [Connect]** per connettersi al server.

Una volta connessi, sono disponibili diverse esperienze:
- **Nuova query**
- **Nuovo notebook**
- **Espandere la visualizzazione del server ed esplorare/lavorare sugli oggetti all'interno del database**
- **...**

## <a name="next-step"></a>Passaggio successivo
[Monitorare il gruppo di server](monitor-grafana-kibana.md)
