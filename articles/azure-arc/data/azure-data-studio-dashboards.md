---
title: Dashboard Azure Data Studio
description: Dashboard Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c2dd0ce3c3e9a0f1f699b58ed2657394ad9acbe1
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939795"
---
# <a name="azure-data-studio-dashboards"></a>Dashboard Azure Data Studio

[Azure Data Studio](https://aka.ms/azuredatastudio) offre un'esperienza simile a quella portale di Azure per la visualizzazione delle informazioni sulle risorse di Azure Arc.  Queste visualizzazioni sono denominate **Dashboard** e hanno un layout e opzioni simili a quelle che è possibile visualizzare su una determinata risorsa nel portale di Azure, ma offrono la flessibilità di visualizzare le informazioni in locale nell'ambiente nei casi in cui non è disponibile una connessione ad Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connecting-to-a-data-controller"></a>Connessione a un controller di dati

### <a name="prerequisites"></a>Prerequisiti

- Scarica [Azure Data Studio](https://aka.ms/getazuredatastudio)
- L'estensione Azure Arc è installata

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>Determinare l'URL dell'endpoint dell'API del server del controller di dati

Prima di tutto, è necessario connettere Azure Data Studio all'URL dell'endpoint dell'API del servizio data controller.

Per ottenere questo endpoint, è possibile eseguire il comando seguente:

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

Verrà visualizzato un output simile al seguente:

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

Se si usa un tipo LoadBalancer, è necessario copiare l'indirizzo IP esterno e il numero di porta 300080.  Se si usa Deport, è possibile usare l'indirizzo IP del server dell'API Kubernetes e il numero di porta elencato sotto la colonna porta/e.

A questo punto, è necessario creare un URL per l'endpoint Combinando queste informazioni come segue:

```console
https://<ip address>:<port>

Example:
https://52.;154.152.24:30080
```

Prendere nota dell'indirizzo IP che verrà usato nel passaggio successivo.

### <a name="connect"></a>Connessione

1. Aprire Azure Data Studio

1. Selezionare la scheda **connessioni** a sinistra

Verso il basso, espandere il pannello denominato **controller di Azure Arc**.

Fare clic sull'icona + per aggiungere una nuova connessione al controller dati.

Nella parte superiore della schermata nel riquadro comandi immettere l'URL creato nel passaggio 1, fare clic su invio.
Immettere il nome utente per il controller dati.  Si tratta del valore del nome utente passato durante la distribuzione del controller di dati.  Fare clic su invio.
Immettere la password per il controller di dati.  Si tratta del valore della password passato durante la distribuzione del controller di dati. Fare clic su invio.

Ora che si è connessi a un controller di dati, è possibile visualizzare i dashboard per il controller dati e qualsiasi istanza gestita di SQL o il gruppo di server di iperscala PostgreSQL.

## <a name="view-the-data-controller-dashboard"></a>Visualizzare il dashboard del controller dati

Fare clic con il pulsante destro del mouse sul controller dati nel pannello connessioni del pannello espandibile **controller di arco** e scegliere **Gestisci**.

Qui è possibile visualizzare i dettagli relativi alla risorsa del controller di dati, ad esempio nome, area, modalità di connessione, gruppo di risorse, sottoscrizione, endpoint controller e spazio dei nomi.  È possibile visualizzare un elenco di tutte le risorse di database gestite gestite anche dal controller dati.

Si noterà che il layout è simile a quello che potrebbe essere visualizzato nel portale di Azure.

In pratica, è possibile avviare la creazione di un gruppo di server di un'istanza gestita di SQL o di un gruppo di server di scalabilità PostgreSQL facendo clic sul pulsante + nuova istanza.

È anche possibile aprire il portale di Azure nel contesto di questo controller dati facendo clic sul pulsante Apri in portale di Azure.

## <a name="view-the-sql-managed-instance-dashboards"></a>Visualizzare i dashboard dell'istanza gestita di SQL

Se sono state create alcune istanze gestite da SQL, è possibile visualizzarle nel pannello connessioni del pannello espandibile di controller di dati di Azure sotto il controller di dati che li gestisce.

Per visualizzare il dashboard dell'istanza gestita di SQL per un'istanza specifica, fare clic con il pulsante destro del mouse sull'istanza e scegliere Gestisci.

Il pannello connessione verrà visualizzato a destra e verrà richiesto l'accesso/password per connettersi all'istanza di SQL. Se si conoscono le informazioni di connessione, è possibile immetterle e fare clic su Connetti.  Se non si è certi, è possibile fare clic su Annulla.  In entrambi i casi, si verrà portati al dashboard quando si chiude il pannello connessione.

Nella scheda Panoramica è possibile visualizzare i dettagli relativi all'istanza gestita di SQL, ad esempio gruppo di risorse, controller dati, ID sottoscrizione, stato, area e altro.  È anche possibile visualizzare il collegamento che è possibile fare clic per accedere ai dashboard Grafana o Kibana nel contesto dell'istanza gestita di SQL.

Se si è in grado di connettersi all'istanza di SQL Manage, è possibile visualizzare altre informazioni qui.

È possibile eliminare l'istanza gestita di SQL da qui o aprire il portale di Azure per visualizzare l'istanza gestita di SQL nel portale di Azure.

Se si fa clic sulla scheda stringhe di connessione a sinistra, è possibile visualizzare un elenco di stringhe di connessione predefinite per l'istanza gestita di SQL, semplificando la copia e incolla in diverse altre applicazioni o codice.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>Visualizzare i dashboard del gruppo di server iperscala PostgreSQL

Se sono stati creati alcuni gruppi di server di scalabilità di PostgreSQL, è possibile visualizzarli nel pannello connessioni nel pannello espandibile di controller di dati di Azure sotto il controller di dati che li gestisce.

Per visualizzare il dashboard del gruppo di server con iperscalabilità PostgreSQL per un gruppo di server specifico, fare clic con il pulsante destro del mouse sul gruppo di server e scegliere Gestisci.

Nella scheda Panoramica è possibile visualizzare i dettagli relativi al gruppo di server, ad esempio gruppo di risorse, controller dati, ID sottoscrizione, stato, area e altro.  È anche possibile visualizzare il collegamento che è possibile fare clic per accedere ai dashboard Grafana o Kibana nel contesto del gruppo di server.

È possibile eliminare il gruppo di server da qui o aprire il portale di Azure per visualizzare il gruppo di server nel portale di Azure.

Se si fa clic sulla scheda stringhe di connessione a sinistra, è possibile visualizzare un elenco di stringhe di connessione predefinite per il gruppo di server, semplificando la copia e incolla in diverse applicazioni o codice.

Se si fa clic sulla scheda proprietà a sinistra, è possibile visualizzare altri dettagli.

Se si fa clic sulla scheda integrità risorsa a sinistra, è possibile visualizzare l'integrità di alto livello corrente del gruppo di server.

Se si fa clic sulla scheda diagnostica e Risolvi i problemi a sinistra, è possibile avviare il notebook per la risoluzione dei problemi di PostgreSQL.

Se si fa clic sulla scheda nuova richiesta di supporto a sinistra, è possibile avviare il portale di Azure nel contesto del gruppo di server e creare una richiesta di supporto di Azure da questa posizione.
