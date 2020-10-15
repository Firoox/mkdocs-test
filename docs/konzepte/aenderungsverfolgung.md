# Änderungsverfolgung

## Einleitung

Die Anwendung soll die Möglichkeit bieten, Änderungen an einer Entität nachverfolgen zu können. Hierzu müssen diese in der Datenbank dokumententiert werden.

Um dies für verschiedene Entitätstypen relativ einfach möglich zu machen, wurde eine typ-unabhängige Lösung implementiert, die bei Bedarf um die Besonderheiten bestimmter Typen ergänzt werden kann.

## Beschreibung

### Interface `ITrackable`

Jede Entität, für die generische Änderungsverfolgung genutzt werden soll, muss das [`ITrackable`](https://github.com/Firoox/pitamy/blob/master/Pitamy.Shared/Interfaces/ITrackable.cs)-Interface implementieren.

Das Interface definiert die Eigenschaften, die zum Speichern wichtige Informationen für die Änderungsverfolgung nötig sind. Außerdem sorgt es dafür, dass die Entität, zusätzlich zum eigentlichen Primary-Key, eine eindeutige Record-ID vom Typ `Guid` erhält.

| Eigenschaft | Typ | Beschreibung |
| ----------- | --- | ------------ |
| **CreatedAt** | `DateTime` | Zeitpunkt, an dem die Entität erstellt wurde. |
| **CreatedBy** | `string` | Name des Benutzers, der die Entität erstellt hat. |
| **LastUpdatedAt** | `DateTime` | Zeitpunkt der letzten Aktualisierung der Entität. |
| **LastUpdatedBy** | `string` | Name des Benutzers, der die Entität zuletzt geändert hat.
| **RecordId** | `Guid` | Ermöglicht es Entitäten verschiedener Typen unabhängig vom eigentlichen Primary-Key zu identifizieren. |

### Datenbank-Tabelle _Aenderung_

Die Änderungen werden in der Datenbank-Tabelle _Aenderung_ gespeichert (Aufbau s. [`ChangelogEntry`](../../datenmodell/aenderung)).

### Entität `ChangelogEntry`

Die Datenbank-Einträge werden innerhalb der Anwendung durch die Klasse [`ChangelogEntry`](../../datenmodell/aenderung) repräsentiert.

### Interface `IDataService` und Implementierung `EFDataService`

Methoden zum Anlegen, Abrufen und Löschen von Einträgen sind im [`IDataService`](https://github.com/Firoox/pitamy/blob/master/Pitamy.Shared/Interfaces/IDataService.cs)-Interface definiert und in der Klasse [`EFDataService`](https://github.com/Firoox/pitamy/blob/master/Pitamy.Data/EFDataService.cs) implementiert.

| Methode | Beschreibung |
| ------- | ------------ |
| `CreateChangelogEntry` | Dient zum Anlegen eines neuen Eintrags in der _Aenderung_-Tabelle |
| `GetChangelogEntries` | Liefert die vorhandenen Einträge zu der übergebenen Record-ID. |
| `DeleteChangelogEntry` | Löscht den Datensatz mit der übergebenen ID. |

!!! warning ""
    Die Methode `DeleteChangelogEntry` dient nur dazu, Einträge zu löschen wenn, z. B. aufgrund einer Exception, die eigentliche Änderung nicht gespeichert wurde! Änderungsdatensätze sollten unter normalen Umständen nicht gelöscht werden (können).

### Änderungserkennung in `ManagerBase`-Klasse

Das Erfassen der Änderungen erfolgt ausschließlich auf der Server-Seite in der verantwortlichen `Manager`-Klasse. Die Basis-Klasse `ManagerBase` bietet die Methode `TrackChanges`, die die Wert der Eigenschaften von zwei Objekten vergleicht und Abweichungen einen neuen Änderungsdatensatz in der Datenbank speichert. 

Eigenschaften, die nicht berücksichtigt werden sollen, können über die Liste `PropertiesIgnoredByTracking` ausgenommen werden.

Wenn der Typ einer Eigenschaft `IEnumerable` implementiert, wird diese, inklusive der enthaltenen Objekte, in einen JSON-String umgewandelt.

!!! warning ""
    Beim Vergleich der JSON-Strings kann es zu Fehlern bei der Erkennung von Änderungen kommen, wenn _Navigation-Properties_ in einem der Objekt geladen wurden und im anderen nicht.

Die `TrackChanges`-Methode sollte in der `Manager`-Klasse im Rahmen der `UpdateEntity`-Methode aufgerufen werden.

### Individuelle Änderungserkennung

Änderungen, die nicht über die `UpdateEntity`-Methode gespeichert werden, können durch Standard-Implementierung nicht erfasst werden!

Ein Beipiel hierfür ist die `Licenses`-Eigenschaft der `Computer`-Klasse. Die enthaltenen `ComputerLicense`-Objekte werden über einen eigenen `Controller`, bzw. `Manager` verwaltet.

Die Erfassung der Änderungen muss daher individuell implementiert werden. Beim beschriebenen Beispiel innerhalb der `ComputerLicensesManager`-Klasse.
Hierbei sollte das Anlegen, Bearbeiten und Löschen der Objekte dokumentiert werden. Die Änderungen sollten den "Haupt"-Entitäten (bei `ComputerLicense` dem Computer und der Lizenz) zugeordnet werden.

!!! warning ""
    Bei der Implementierung unbedingt die Hinweise zur Verwendung der `ChangelogEntry`-Eigenschaften `OldValue` und `NewValue` beachten!

### Anzeige auf dem Client

Für die Anzeige der Änderungen an einer Entität stellt der Client den Dialog `ChangelogDialogView` bereit. Der Aufruf erfolgt das client-interne Nachrichtensystem:

```csharp
DialogRequestMessage message = new DialogRequestMessage<ITrackable>(Computer, ClientMessages.ChangelogShowDialog);

message.OwnerViewModel = this;

Messenger.Default.Send(message, MessengerTokens.SharedDialogService);
```

Die Nachricht wird von der Implementierung des `IShellDialogService`-Interfaces (`ShellDialogService`) verarbeitet.

Der Abruf der Änderungen erfolgt über den `ShellDataService` (`IShellDataService`).

### Anpassung der Darstellung

Der Changelog-Dialog kann u. a. komplexe Werte, die als JSON gespeichert wurden, nicht in einer lesbaren Form darstellen. 

Für die Individualisierung der Darstellung besteht daher die Möglichkeit für einen Entitätstypen eine Implementierung des `IChangelogConfiguration`-Interfaces über den IoC-Container bereitzustellen.

| Methode | Beschreibung |
| ------- | ------------ |
| `CheckIgnoredProperties` | Ermöglicht es, Änderungen an bestimmten Eigenschaften (z. B. Ids) nicht anzuzeigen |
| `FormatValue` | Formatiert den in der Datenbank abgelegten String-Wert in eine lesbare Form |
| `GetPropertyDisplayName` | Liefert für den deutschen bzw. englischen Text für die Eigenschaft |

```csharp
IStringResources stringResources = SimpleIoc.Default.GetInstance<IStringResources>();

SimpleIoc.Default.Register<IChangelogConfiguration>(() => new ComputerChangelogConfiguration(stringResources), nameof(Computer));
```

Das `ChangelogDialogViewModel` lädt die passende Configuration, falls vorhanden, nach dem Abruf der Änderungen aus der Datenbank.

<figure>
  <img src="../ChangelogDialogView.png" alt="Changelog-Dialog für einen Computer" />
  <figcaption>Changelog-Dialog für einen Computer</figcaption>
</figure>
