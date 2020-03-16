---
title: Usare la configurazione dinamica in un'app Spring Boot
titleSuffix: Azure App Configuration
description: Informazioni su come aggiornare dinamicamente i dati di configurazione per le app Spring Boot
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 6445b9707273d273c562b7d643da34f5ba26e1fc
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967493"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Esercitazione: Usare la configurazione dinamica in un'app Java Spring

La libreria client Spring Boot di Configurazione app supporta l'aggiornamento su richiesta di un set di impostazioni di configurazione senza causare il riavvio di un'applicazione. La libreria client memorizza nella cache ogni impostazione per evitare un numero eccessivo di chiamate all'archivio di configurazione. Finché il valore memorizzato nella cache non scade, l'operazione di aggiornamento non aggiorna il valore, neanche se è cambiato nell'archivio di configurazione. Il tempo di scadenza predefinito per ogni richiesta è 30 secondi. È possibile sostituire tale valore se necessario.

È possibile verificare la presenza di impostazioni aggiornate su richiesta chiamando il metodo `refreshConfigurations()` di `AppConfigurationRefresh`.

In alternativa, è possibile usare il pacchetto `spring-cloud-azure-appconfiguration-config-web`, che consente a una dipendenza in `spring-web` di gestire l'aggiornamento automatico.

## <a name="use-automated-refresh"></a>Usare l'aggiornamento automatico

Per usare l'aggiornamento automatico, iniziare con un'app Spring Boot che usa Configurazione app, ad esempio l'app creata seguendo la guida di avvio rapido per la creazione di un'app [Spring Boot con Configurazione app](quickstart-java-spring-app.md).

Aprire quindi il file *pom.xml* in un editor di testo e aggiungere un elemento `<dependency>` per `spring-cloud-azure-appconfiguration-config-web`.

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Salvare il file, quindi compilare ed eseguire l'applicazione come di consueto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata abilitata l'app Spring Boot per aggiornare in modo dinamico le impostazioni di configurazione da Configurazione app. Per altre informazioni, vedere [Spring in Azure](https://docs.microsoft.com/java/azure/spring-framework/).
