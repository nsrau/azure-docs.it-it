
1. Assicurarsi di arrestare il servizio mobile se è in esecuzione in IIS Express. Fare clic con il pulsante destro del mouse sull'icona IIS Express sulla barra delle applicazioni e scegliere **stop** per il servizio mobile

    ![](./media/mobile-services-how-to-configure-iis-express/iis-express-tray-stop-site.png)


2. In una finestra del prompt dei comandi eseguire il comando **ipconfig** per cercare un indirizzo IP locale valido per la workstation.

    ![](./media/mobile-services-how-to-configure-iis-express/ipconfig.png)


3. In Visual Studio aprire il file applicationhost.config per IIS Express. Questo file si trova nella sottodirectory seguente della directory del profilo utente.

        C:\Users<your profile name>\Documents\IISExpress\config\applicationhost.config

4. Configurare IIS Express per consentire le richieste di connessioni remota al servizio. A questo scopo, nel file applicationhost.config trovare l'elemento relativo al sito per il servizio mobile e aggiungere un nuovo elemento `binding` per la porta usando l'indirizzo IP individuato in precedenza. Salvare il file applicationhost.config.

    L'elemento relativo al sito aggiornato dovrebbe essere simile a quanto segue:

        <site name="todolist_Service(1)" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="C:\Archive\GetStartedDataWP8\C#\todolist_Service" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:58203:localhost" />
                <binding protocol="http" bindingInformation="*:58203:192.168.137.72" />
            </bindings>
        </site>

5. Aprire la console di Windows Firewall e creare una nuova regola di porta per consentire le connessioni alla porta. Per ulteriori informazioni sulla creazione di una nuova regola di porta in Windows Firewall, vedere l'argomento relativo all'[aggiunta di una nuova regola porta in Windows Firewall].

    >[AZURE.NOTE]Se il computer di test fa parte di un dominio, è possibile che le eccezioni del firewall siano controllate da un criterio di dominio. In questo caso, contattare l'amministratore del dominio per ottenere un'esenzione per la porta nel computer in uso.

    A questo punto il computer dovrebbe essere configurato per il test con l'istanza di IIS Express che ospita il servizio mobile.

    >[AZURE.NOTE]Al termine del test del servizio in locale, eliminare la regola di Windows Firewall creata.


<!-- URLs. -->
[aggiunta di una nuova regola porta in Windows Firewall]: http://go.microsoft.com/fwlink/?LinkId=392240

<!---HONumber=July15_HO3-->