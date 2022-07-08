# Exporting/Importing data

Using the built-in Django management commands, one can dump or load
data directly from JSON or YAML files.

## Exporting

To dump data in JSON format:

```bash
python3 manage.py dumpdata <specific table or app name> > filename.json
```

For example, for dumping all the `remotescripts` configuration of CertHelper:

```bash
python3 manage.py dumpdata remotescripts > remotescripts.json
```

## Importing data

To import data from a JSON file:

```bash
python3 manage.py loaddata <json file>
```

e.g.:

```bash
python3 manage.py loaddata remotescripts.json
```
