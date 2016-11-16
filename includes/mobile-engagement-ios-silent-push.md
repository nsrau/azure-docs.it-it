> [!IMPORTANT]
> Per ricevere le notifiche push da Mobile Engagement, è necessario abilitare `Silent Remote Notifications` nell'applicazione. È necessario aggiungere il valore di notifica remota alla matrice UIBackgroundModes nel file Info.plist.
> 
> 

1. Aprire il file `info.plist` del progetto.
2. Fare clic con il pulsante destro del mouse sul primo elemento dell'elenco (`Information Property List`) e aggiungere una nuova riga.
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)
3. Nella nuova riga immettere `Required background modes`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)
4. Fare clic sulla freccia sinistra per espandere la riga.
5. Aggiungere il seguente valore all'elemento 0 `App downloads content in response to push notifications`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)
6. Dopo avere apportato la modifica, info.plist XML deve contenere la chiave e il valore seguenti:
   
        <key>UIBackgroundModes</key>
        <array>
        <string>remote-notification</string>
        </array>
7. Se si usano **Xcode 7+** e **iOS 9+**:
   
   * Abilitare **Notifiche push** in Destinazioni > Nome destinazione > Funzionalità.



<!--HONumber=Nov16_HO2-->


