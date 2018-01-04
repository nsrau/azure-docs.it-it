## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge consente la distribuzione basata su cloud di codice specifico della soluzione per i dispositivi locali e i servizi Azure. I dispositivi IoT Edge possono aggregare dati da altri dispositivi per eseguire operazioni di elaborazione e analisi prima che i dati vengano inviati al cloud. Per altre informazioni, vedere [Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agente di IoT Edge
La parte del runtime di IoT Edge responsabile della distribuzione e del monitoraggio dei moduli.

## <a name="iot-edge-device"></a>Dispositivo IoT Edge
Nei dispositivi IoT Edge è installato il runtime IoT. Nei dettagli dei dispositivi sono contrassegnati come "Dispositivo IoT Edge". Altre informazioni sono disponibili in [Distribuire Azure IoT Edge in un dispositivo simulato in Linux: anteprima](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-deployment"></a>Distribuzione di IoT Edge
Una distribuzione di IoT Edge configura un set di destinazione di dispositivi IoT Edge per l'esecuzione di un set di moduli IoT Edge. Ogni distribuzione garantisce continuamente che tutti i dispositivi conformi alla condizione di destinazione eseguano il set di moduli specificato, anche quando vengono creati nuovi dispositivi o vengono modificati in modo che corrispondano alla condizione di destinazione. Ogni dispositivo IoT Edge riceve solo la distribuzione con priorità più alta tra quelle di cui soddisfa la condizione di destinazione. Per altre informazioni, vedere [Distribuzione di IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifesto della distribuzione di IoT Edge
Documento Json contenente le informazioni da copiare in uno o più moduli gemelli di dispositivi IoT Edge per distribuire un set di moduli, route e le proprietà desiderate del modulo associato.

## <a name="iot-edge-gateway-device"></a>Dispositivo gateway IoT Edge
Un dispositivo IoT Edge con dispositivo downstream, che può essere un dispositivo IoT Edge o non IoT Edge.

## <a name="iot-edge-hub"></a>Hub di IoT Edge
La parte del runtime di IoT Edge responsabile delle comunicazioni da modulo a modulo e delle comunicazioni upstream (verso l'hub IoT) e downstream (dall'hub IoT). 

## <a name="iot-edge-leaf-device"></a>Dispositivo foglia IoT Edge
Un dispositivo IoT Edge senza dispositivo downstream. 

## <a name="iot-edge-module"></a>Modulo IoT Edge
Un modulo IoT Edge è un contenitore Docker che è possibile distribuire su dispositivi IoT Edge. Esegue un'attività specifica, come inserire un messaggio da un dispositivo, trasformare un messaggio o inviare un messaggio a un hub IoT. Comunica inoltre con altri moduli e invia dati al runtime di IoT Edge. [Informazioni sui requisiti e gli strumenti per sviluppare moduli IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Identità del modulo IoT Edge
Un record nel registro delle identità del modulo dell'hub IoT con informazioni dettagliate sull'esistenza e le credenziali di sicurezza che devono essere usate da un modulo per eseguire l'autenticazione con un hub Edge o l'hub IoT.

## <a name="iot-edge-module-image"></a>Immagine del modulo IoT Edge
L'immagine Docker usata dal runtime di IoT Edge per creare istanze del modulo.

## <a name="iot-edge-module-twin"></a>Modulo IoT Edge gemello
Un documento Json contenuto nell'hub IoT in cui vengono archiviate le informazioni di stato relative a un'istanza del modulo.

## <a name="iot-edge-priority"></a>Priorità IoT Edge
Quando due distribuzioni di IoT Edge hanno come destinazione lo stesso dispositivo, viene applicata la distribuzione con priorità più elevata. Se due distribuzioni hanno la stessa priorità, viene applicata la distribuzione con la data di creazione successiva. Per altre informazioni, vedere [Priorità](https://docs.microsoft.com/en-us/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Runtime di IoT Edge
Il runtime di IoT Edge include tutti i componenti distribuiti da Microsoft per essere installati in un dispositivo IoT Edge. In particolare, sono inclusi un agente Edge, un hub Edge e uno strumento CTL Edge.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge imposta i moduli in un unico dispositivo
Operazione che copia il contenuto di un file manifesto di IoT Edge in un modulo gemello del dispositivo. L'API sottostante è del tipo generico "applica configurazione", ovvero si limita ad accettare un file manifesto di IoT Edge come input.

## <a name="iot-edge-target-condition"></a>Condizione di destinazione IoT Edge
In una distribuzione di IoT Edge, la condizione di destinazione è una qualsiasi condizione booleana presente nei tag di dispositivi gemelli che consente di selezionare i dispositivi di destinazione della distribuzione, ad esempio, "tag.environment = prod". La condizione di destinazione viene costantemente valutata in modo da includere eventuali nuovi dispositivi conformi ai requisiti o rimuovere dispositivi non più conformi. Per altre informazioni, vedere [Condizione di destinazione](https://docs.microsoft.com/en-us/azure/iot-edge/module-deployment-monitoring#target-condition)