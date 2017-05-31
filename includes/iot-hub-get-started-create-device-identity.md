## <a name="create-a-device-identity"></a>Creare un'identità del dispositivo

In questa sezione si usa uno strumento di Node.js denominato [iothub-explorer][iot-hub-explorer] per creare un'identità del dispositivo per questa esercitazione.

1. Eseguire il codice seguente nell'ambiente della riga di comando:

    `npm install -g iothub-explorer@latest`

1. Eseguire quindi il comando indicato di seguito per accedere all'hub. Sostituire `{iot hub connection string}` con la stringa di connessione all'hub IoT copiata prima:

    `iothub-explorer login "{iot hub connection string}"`

1. Creare infine una nuova identità del dispositivo denominata `myDeviceId` con il comando:

    `iothub-explorer create myDeviceId --connection-string`

Annotare la stringa di connessione del dispositivo visualizzata nei risultati. Questa stringa di connessione del dispositivo viene usata dall'app del dispositivo per la connessione all'hub IoT come dispositivo.

![][img-identity]

Per creare identità del dispositivo a livello di codice, vedere [Introduzione all'hub IoT][lnk-getstarted].

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
