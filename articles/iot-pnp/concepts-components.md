---
title: Informazioni sui componenti nei modelli Plug and Play Microsoft Docs
description: Comprendere la differenza tra le cose Plug and Play modelli DTDL che utilizzano componenti e modelli che non utilizzano componenti.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4c41edc477460e6d239688aafe6d7219bed36cd4
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352322"
---
# <a name="iot-plug-and-play-components-in-models"></a>Componenti Plug and Play nei modelli

Per quanto riguarda le convenzioni Plug and Play, un dispositivo è molto Plug and Play dispositivo se presenta l'ID del modello DTDL (Digital Twin Definition Language) quando si connette a un hub.

Il frammento di codice seguente mostra alcuni esempi di ID modello:

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Nessun componente

Un modello semplice non usa componenti incorporati o A cascata. Include informazioni di intestazione e una sezione di contenuto per definire i dati di telemetria, le proprietà e i comandi.

Nell'esempio seguente viene illustrata una parte di un modello semplice che non utilizza i componenti di:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Anche se il modello non definisce in modo esplicito un componente, si comporta come se fosse presente un singolo componente con tutte le definizioni dei dati di telemetria, della proprietà e del comando.

Lo screenshot seguente mostra il modo in cui il modello viene visualizzato nello strumento Esplora risorse di Azure:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Componente predefinito in Azure Internet Explorer":::

L'ID modello viene archiviato in una proprietà del dispositivo gemello, come illustrato nella schermata seguente:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="ID modello nella proprietà del dispositivo gemello digitale":::

Un modello DTDL senza componenti è una semplificazione utile per un dispositivo con un unico set di dati di telemetria, proprietà e comandi. Un modello che non usa componenti consente di eseguire facilmente la migrazione di un dispositivo esistente come un Plug and Play dispositivo. si crea un modello DTDL che descrive il dispositivo effettivo senza la necessità di definire alcun componente.

## <a name="multiple-components"></a>Più componenti

I componenti consentono di creare un'interfaccia del modello come assembly di altre interfacce.

Ad esempio, l'interfaccia del [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) è definita come modello. È possibile incorporare questa interfaccia come uno o più componenti quando si definisce il [modello di controller di temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). Nell'esempio seguente questi componenti sono denominati `thermostat1` e `thermostat2` .

Per un modello DTDL con più componenti, sono presenti due o più sezioni di componenti. Ogni sezione è `@type` impostata su `Component` e fa riferimento in modo esplicito a uno schema, come illustrato nel frammento di codice seguente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
... 
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
...
```

Questo modello include tre componenti definiti nella sezione contenuto, due `Thermostat` componenti e un `DeviceInformation` componente. Esiste anche un componente radice predefinito.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese informazioni sui componenti del modello, di seguito sono riportate alcune risorse aggiuntive:

- [Digital Gemini Definition Language V2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Repository di modelli](./concepts-model-repository.md)