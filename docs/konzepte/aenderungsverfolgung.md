# Änderungsverfolgung

## Einleitung

Die Anwendung soll die Möglichkeit bieten, Änderungen an einer Entität nachverfolgen zu können. Hierzu müssen diese in der Datenbank dokumententiert werden.

Um dies für verschiedene Entitätstypen relativ einfach möglich zu machen, wurde eine typ-unabhängige Lösung implementiert, die bei Bedarf um die Besonderheiten bestimmter Typen ergänzt werden kann.

## Interface `ITrackable`

Jede Entität, für die generische Änderungsverfolgung genutzt werden soll, muss das [`ITrackable`](https://github.com/Firoox/pitamy/blob/master/Pitamy.Shared/Interfaces/ITrackable.cs)-Interface implementieren.

Das Interface definiert die Eigenschaften, die zum Speichern wichtige Informationen für die Änderungsverfolgung nötig sind. Außerdem sorgt es dafür, dass die Entität, zusätzlich zum eigentlichen Primary-Key, eine eindeutige Record-ID vom Typ `Guid` erhält.

| Eigenschaft | Typ | Beschreibung |
| ----------- | --- | ------------ |
| **CreatedAt** | `DateTime` | Zeitpunkt, an dem die Entität erstellt wurde. |
| **CreatedBy** | `string` | Name des Benutzers, der die Entität erstellt hat. |
| **LastUpdatedAt** | `DateTime` | Zeitpunkt der letzten Aktualisierung der Entität. |
| **LastUpdatedBy** | `string` | Name des Benutzers, der die Entität zuletzt geändert hat.
| **RecordId** | `Guid` | Ermöglicht es Entitäten verschiedener Typen unabhängig vom eigentlichen Primary-Key zu identifizieren. |

