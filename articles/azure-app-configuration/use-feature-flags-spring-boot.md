---
title: Esercitazione per l'uso di flag di funzionalità in un'app Spring Boot - Configurazione app di Azure | Microsoft Docs
description: Questa esercitazione illustra come implementare i flag di funzionalità nelle app Spring Boot.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: 8c66e2995462701f7ddaefc3a2623c02fee883ef
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687201"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Esercitazione: Usare i flag di funzionalità in un'app Spring Boot

Le librerie di gestione delle funzionalità di base di Spring Boot offrono supporto per l'implementazione dei flag di funzionalità in un'applicazione Spring Boot. Queste librerie consentono di aggiungere flag di funzionalità al codice in modo dichiarativo.

Le librerie di gestione delle funzionalità gestiscono anche i cicli di vita dei flag di funzionalità in background. Ad esempio, le librerie aggiornano e memorizzano nella cache gli stati del flag o garantiscono lo stato immutabile di un flag durante una chiamata di richiesta. La libreria Spring Boot offre anche integrazioni, tra cui azioni del controller MVC, route e middleware.

La guida di avvio rapido [Aggiungere flag di funzionalità a un'app Spring Boot](./quickstart-feature-flag-spring-boot.md) illustra diversi modi per aggiungere flag di funzionalità in un'applicazione Spring Boot. Questa esercitazione illustra questi metodi più dettagliatamente.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere i flag di funzionalità nelle parti chiave dell'applicazione per controllare la disponibilità delle funzionalità.
> * Eseguire l'integrazione con Configurazione app quando viene usato per gestire i flag di funzionalità.

## <a name="set-up-feature-management"></a>Configurare la gestione delle funzionalità

Lo strumento di gestione delle funzionalità di Spring Boot `FeatureManager` ottiene i flag di funzionalità dal sistema di configurazione nativo del framework. Di conseguenza, è possibile definire i flag di funzionalità dell'applicazione usando qualsiasi origine di configurazione supportata da Spring Boot, ad esempio il file locale *bootstrap.yml* o le variabili di ambiente. `FeatureManager` si basa sull'inserimento delle dipendenze. È possibile registrare i servizi di gestione di funzionalità usando le convenzioni standard:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

È consigliabile mantenere i flag di funzionalità all'esterno dell'applicazione e gestirli separatamente. In questo modo è possibile modificare gli stati dei flag in qualsiasi momento e applicare immediatamente tali modifiche nell'applicazione. Configurazione app offre una posizione centralizzata per organizzare e controllare tutti i flag di funzionalità tramite un'interfaccia utente del portale dedicata. Configurazione app fornisce anche i flag direttamente all'applicazione tramite le relative librerie client Spring Boot.

Il modo più semplice per connettere l'applicazione Spring Boot a Configurazione app consiste nell'usare il provider di configurazione:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
    <version>1.1.0.M4</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Dichiarazione dei flag di funzionalità

Ogni flag di funzionalità è costituito da due parti: un nome e un elenco di uno o più filtri che consentono di valutare se lo stato di una funzionalità è *attivo* (ovvero, quando il relativo valore è `True`). Un filtro definisce un caso d'uso per il quale deve essere attivata una funzionalità.

Se un flag di funzionalità ha più filtri, l'elenco dei filtri viene attraversato fino a quando uno dei filtri non determina che la funzionalità deve essere abilitata. A questo punto, il flag di funzionalità è *attivo* e i risultati del filtro rimanenti vengono ignorati. Se nessun filtro indica che deve essere abilitata la funzionalità, il flag di funzionalità viene *disattivato*.

Lo strumento di gestione delle funzionalità supporta *application.yml* come origine di configurazione per i flag di funzionalità. L'esempio seguente illustra come configurare i flag di funzionalità in un file YAML:

```yml
feature-management:
  featureSet:
    features:
      FeatureA: true
      FeatureB: false
      FeatureC:
        EnabledFor:
          -
            name: Percentage
            parameters:
              value: 50
```

Per convenzione, la sezione `feature-management` di questo documento YML viene usata per le impostazioni dei flag di funzionalità. L'esempio precedente mostra tre flag di funzionalità con i relativi filtri definiti nella proprietà `EnabledFor`:

* `FeatureA` è *attivo*.
* `FeatureB` è *disattivato*.
* `FeatureC` specifica un filtro denominato `Percentage` con una proprietà `Parameters`. `Percentage` è un filtro configurabile. In questo esempio `Percentage` specifica una probabilità del 50% che il flag `FeatureC` sia *attivo*.

## <a name="feature-flag-checks"></a>Controlli dei flag di funzionalità

Il modello di base di gestione delle funzionalità consiste prima di tutto nel controllare se un flag di funzionalità è impostato su *attivo*. In tal caso, la gestione delle funzionalità esegue le azioni contenute nella funzionalità. Ad esempio:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabled("FeatureA"))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Inserimento delle dipendenze

In Spring Boot è possibile accedere allo strumento di gestione delle funzionalità `FeatureManager` tramite l'inserimento delle dipendenze:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Azioni del controller

Nei controller MVC è possibile usare l'attributo `@FeatureGate` per controllare se un'azione specifica è abilitata. Per poter eseguire l'azione `Index` seguente, il flag`FeatureA` deve essere *attivo*:

```java
@GetMapping("/")
@FeatureGate(feature = "FeatureA")
public String index(Model model) {
    ...
}
```

Quando un controller o un'azione MVC viene bloccato perché il flag di funzionalità di controllo è *disattivato*, viene chiamata un'interfaccia `IDisabledFeaturesHandler` registrata. L'interfaccia `IDisabledFeaturesHandler` predefinita restituisce un codice di stato 404 al client senza alcun corpo della risposta.

## <a name="mvc-filters"></a>Filtri MVC

È possibile configurare i filtri MVC in modo da attivarli in base allo stato di un flag di funzionalità. Il codice seguente aggiunge un filtro MVC denominato `FeatureFlagFilter`. Questo filtro viene attivato all'interno della pipeline MVC solo se il flag `FeatureA` è attivato.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabled("FeatureA")) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Route

È possibile usare i flag di funzionalità per reindirizzare le route. Il codice seguente abiliterà il reindirizzamento di un utente da `FeatureA`:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "FeatureA", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come implementare i flag di funzionalità in un'applicazione Spring Boot usando le librerie `spring-cloud-azure-feature-management-web`. Per altre informazioni sul supporto della gestione delle funzionalità in Spring Boot e Configurazione app, vedere le risorse seguenti:

* [Codice di esempio per i flag di funzionalità in Spring Boot](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Gestire i flag di funzionalità](./manage-feature-flags.md)
