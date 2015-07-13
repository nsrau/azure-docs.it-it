
È quindi necessario modificare la modalità di registrazione delle notifiche push, in modo che l'utente sia autenticato prima del tentativo di registrazione.

1. In **QSAppDelegate.m** rimuovere completamente l'implementazione di **didFinishLaunchingWithOptions**.

2. Aprire **QSTodoListViewController.m** e, alla fine del metodo **viewDidLoad**, aggiungere il codice seguente:

```
// Register for remote notifications
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
```

<!---HONumber=62-->