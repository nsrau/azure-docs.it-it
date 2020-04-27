---
title: "Esercitazione: Connettere un'app client Python generica ad Azure IoT Central | Microsoft Docs"
description: Questa esercitazione illustra agli sviluppatori di dispositivi come connettere un dispositivo che esegue un'app client Python all'applicazione Azure IoT Central. Per creare un modello di dispositivo, importare un modello di funzionalità di dispositivo e aggiungere visualizzazioni che consentono di interagire con un dispositivo connesso
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d6c44c81db78fa76eeaf4b7181cca34fb8e81523
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758178"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Esercitazione: Creare e connettere un'applicazione client all'applicazione Azure IoT Central (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Le informazioni di questo articolo sono destinate a generatori di soluzioni e sviluppatori di dispositivi.*

Questa esercitazione illustra agli sviluppatori di dispositivi come connettere un'applicazione client Python all'applicazione Azure IoT Central. L'applicazione Python simula il comportamento di un dispositivo sensore ambientale reale. Verrà usato un _modello di funzionalità di dispositivo_ di esempio per creare un _modello di dispositivo_ in IoT Central. È possibile aggiungere visualizzazioni al modello di dispositivo per consentire a un operatore di interagire con un dispositivo.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Importare un modello di funzionalità di dispositivo per creare un modello di dispositivo.
> * Aggiungere visualizzazioni predefinite e personalizzate a un modello di dispositivo.
> * Pubblicare un modello di dispositivo e aggiungere un dispositivo reale all'applicazione IoT Central.
> * Creare ed eseguire il codice del dispositivo Python per connetterlo all'applicazione IoT Central.
> * Visualizzare i dati di telemetria simulati inviati dal dispositivo.
> * Usare una visualizzazione per gestire le proprietà del dispositivo.
> * Chiamare i comandi sincroni e asincroni per controllare il dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

* Un'applicazione Azure IoT Central creata usando il modello **Applicazione personalizzata**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
* Un computer di sviluppo con [Python](https://www.python.org/) versione 3.7 o successiva installato. Per controllare la versione, è possibile eseguire `python3 --version` nella riga di comando. Python è disponibile per un'ampia gamma di sistemi operativi. Le istruzioni riportate in questa esercitazione presuppongono che il comando **python3** venga eseguito al prompt dei comandi di Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Creare un'applicazione Python

I passaggi seguenti illustrano come creare un'applicazione client Python che si connette al dispositivo reale aggiunto all'applicazione. L'applicazione Python simula il comportamento di un dispositivo reale.

1. Nell'ambiente della riga di comando passare alla cartella `environmental-sensor` creata in precedenza.

1. Per installare le librerie necessarie, eseguire i comandi seguenti:

    ```cmd
    pip install azure-iot-device
    ```

1. Creare un file denominato **environmental_sensor.py** nella cartella `environmental-sensor`.

1. Aggiungere le istruzioni `import` seguenti all'inizio del file **environmental_sensor.py**:

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. Aggiungere le dichiarazioni di variabili e la funzione `main` asincrona seguenti al file:

    ```python
    async def main():
      # In a production environment, don't store
      # connection information in the code.
      provisioning_host = 'global.azure-devices-provisioning.net'
      id_scope = '{your Scope ID}'
      registration_id = '{your Device ID}'
      symmetric_key = '{your Primary Key}'

      delay = 2

      # All the remaining code is nested within this main function

    if __name__ == '__main__':
    asyncio.run(main())
    ```

    Aggiornare i segnaposto `{your Scope ID}`, `{your Device ID}` e `{your Primary Key}` con i valori annotati in precedenza. In un'applicazione reale non è necessario includere queste informazioni hardcoded nell'applicazione.

    Tutte le definizioni di funzione e il codice seguenti sono annidati all'interno della funzione `main`.

1. Aggiungere le due funzioni seguenti all'interno della funzione `main` per registrare il dispositivo e connetterlo all'applicazione IoT Central. La registrazione usa il Servizio Device Provisioning di Azure:

    ```python
      async def register_device():
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
          provisioning_host=provisioning_host,
          registration_id=registration_id,
          id_scope=id_scope,
          symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print(f'Registration result: {registration_result.status}')

        return registration_result

      async def connect_device():
        device_client = None
        try:
          registration_result = await register_device()
          if registration_result.status == 'assigned':
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
              symmetric_key=symmetric_key,
              hostname=registration_result.registration_state.assigned_hub,
              device_id=registration_result.registration_state.device_id,
            )
            # Connect the client.
            await device_client.connect()
            print('Device connected successfully')
        finally:
          return device_client
    ```

1. Per inviare i dati di telemetria all'applicazione IoT Central, aggiungere la funzione seguente all'interno della funzione `main`:

    ```python
      async def send_telemetry():
        print(f'Sending telemetry from the provisioned device every {delay} seconds')
        while True:
          temp = random.randrange(1, 75)
          humid = random.randrange(30, 99)
          payload = json.dumps({'temp': temp, 'humid': humid})
          msg = Message(payload)
          await device_client.send_message(msg, )
          print(f'Sent message: {msg}')
          await asyncio.sleep(delay)
    ```

    I nomi degli elementi di telemetria (`temp` e `humid`) devono corrispondere ai nomi usati nel modello di dispositivo.

1. Aggiungere le funzioni seguenti all'interno della funzione `main` per gestire i comandi chiamati dall'applicazione IoT Central:

    ```python
      async def blink_command(request):
        print('Received synchronous call to blink')
        response = MethodResponse.create_from_method_request(
          request, status = 200, payload = {'description': f'Blinking LED every {request.payload} seconds'}
        )
        await device_client.send_method_response(response)  # send response
        print(f'Blinking LED every {request.payload} seconds')

      async def diagnostics_command(request):
        print('Starting asynchronous diagnostics run...')
        response = MethodResponse.create_from_method_request(
          request, status = 202
        )
        await device_client.send_method_response(response)  # send response
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Sending property update to confirm command completion')
        await device_client.patch_twin_reported_properties({'rundiagnostics': {'value': f'Diagnostics run complete at {datetime.datetime.today()}.'}})

      async def turnon_command(request):
        print('Turning on the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      async def turnoff_command(request):
        print('Turning off the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      commands = {
        'blink': blink_command,
        'rundiagnostics': diagnostics_command,
        'turnon': turnon_command,
        'turnoff': turnoff_command,
      }

      # Define behavior for handling commands
      async def command_listener():
        while True:
          method_request = await device_client.receive_method_request()  # Wait for commands
          await commands[method_request.name](method_request)
    ```

    I nomi dei comandi (`blink`, `turnon`, `turnoff` e `rundiagnostics`) devono corrispondere ai nomi usati nel modello di dispositivo.

    Attualmente, IoT Central non usa lo schema di risposta definito nel modello di funzionalità di dispositivo. Per un comando sincrono, il payload della risposta può essere qualsiasi JSON valido. Per un comando asincrono, il dispositivo deve restituire immediatamente una risposta 202, seguita dall'aggiornamento della proprietà segnalata al termine dell'attività. Il formato dell'aggiornamento della proprietà segnalata è:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Un operatore può visualizzare il payload della risposta nella cronologia dei comandi.

1. Aggiungere le funzioni seguenti all'interno della funzione `main` per gestire gli aggiornamenti delle proprietà inviati dall'applicazione IoT Central:

    ```python
        async def name_setting(value, version):
          await asyncio.sleep(1)
          print(f'Setting name value {value} - {version}')
          await device_client.patch_twin_reported_properties({'name' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        async def brightness_setting(value, version):
          await asyncio.sleep(5)
          print(f'Setting brightness value {value} - {version}')
          await device_client.patch_twin_reported_properties({'brightness' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        settings = {
          'name': name_setting,
          'brightness': brightness_setting
        }

        # define behavior for receiving a twin patch
        async def twin_patch_listener():
          while True:
            patch = await device_client.receive_twin_desired_properties_patch() # blocking
            to_update = patch.keys() & settings.keys()
            await asyncio.gather(
              *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
            )
    ```

    Quando l'operatore imposta una proprietà scrivibile nell'applicazione IoT Central, l'applicazione usa una proprietà desiderata del dispositivo gemello per inviare il valore al dispositivo. Il dispositivo risponde quindi usando una proprietà segnalata dal dispositivo gemello. Quando IoT Central riceve il valore della proprietà segnalata, aggiorna la visualizzazione delle proprietà con lo stato **sincronizzato**.

    I nomi delle proprietà (`name` e `brightness`) devono corrispondere ai nomi usati nel modello di dispositivo.

1. Aggiungere le funzioni seguenti all'interno della funzione `main` per controllare l'applicazione:

    ```python
      # Define behavior for halting the application
      def stdin_listener():
        while True:
          selection = input('Press Q to quit\n')
          if selection == 'Q' or selection == 'q':
            print('Quitting...')
            break

      device_client = await connect_device()

      if device_client is not None and device_client.connected:
        print('Send reported properties on startup')
        await device_client.patch_twin_reported_properties({'state': 'true'})
        tasks = asyncio.gather(
          send_telemetry(),
          command_listener(),
          twin_patch_listener(),
        )

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)

        # Wait for user to indicate they are done listening for method calls
        await user_finished

        # Cancel tasks
        tasks.add_done_callback(lambda r: r.exception())
        tasks.cancel()
        await device_client.disconnect()

      else:
        print('Device could not connect')
    ```

1. Salvare il file **environmental_sensor.py**.

## <a name="run-your-python-application"></a>Eseguire l'applicazione Python

Per avviare l'applicazione client del dispositivo, immettere il comando seguente nell'ambiente della riga di comando:

```cmd
python3 environmental_sensor.py
```

È possibile osservare che il dispositivo si connette all'applicazione Azure IoT Central e inizia a inviare i dati di telemetria:

![Eseguire l'applicazione client](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

È possibile visualizzare il modo in cui il dispositivo risponde ai comandi e agli aggiornamenti delle proprietà:

![Osservare l'applicazione client](media/tutorial-connect-device-python/run-application-2.png)

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base relative alla creazione di un dispositivo con Node.js, i passaggi successivi consigliati per gli sviluppatori di dispositivi sono i seguenti:

- Articolo procedurale [Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central](./howto-connect-devkit.md) per informazioni su come connettere un dispositivo reale a IoT Central.
- Articolo [Connettersi ad Azure IoT Central](./concepts-get-connected.md) per altre informazioni su come registrare dispositivi con IoT Central e sulla protezione delle connessioni ai dispositivi in IoT Central.

Se si preferisce continuare il set di esercitazioni di IoT Central e saperne di più sulla creazione di una soluzione di IoT Central, vedere:

> [!div class="nextstepaction"]
> [Creare un modello di dispositivo gateway](./tutorial-define-gateway-device-type.md)