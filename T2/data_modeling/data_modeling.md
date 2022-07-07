# Data Modeling Approach

## Glosario

- **Modelo:** La definición formal de un tipo de activo.
- **Activo:** Cualquier tipo de entidad digilmente representada en la plataforma. Pueden ser físicos, como personas, sensores o máquinas o abstractos como productos, procesos, aplicaciones, etc.
- **Esquema:** La implementación interna de un modelo.

## Estructura básica

Los modelos son definidos usando JavaScript Object Notation (JSON). La estructura mínima necesaria para definir un modelo tendría el siguiente aspecto:

```bash
schema = {
    'firstname': {
        'type': 'string',
        'minlength': 1,
        'maxlength': 10,
    },
    'lastname': {
        'type': 'string',
        'minlength': 1,
        'maxlength': 15,
        'required': True,
        'unique': True,
    },
    # 'role' is a list, and can only contain values from 'allowed'.
    'role': {
        'type': 'list',
        'allowed': ["author", "contributor", "copy"],
    },
    # An embedded 'strongly-typed' dictionary.
    'location': {
        'type': 'dict',
        'schema': {
            'address': {'type': 'string'},
            'city': {'type': 'string'}
        },
    },
    'born': {
        'type': 'datetime',
    },
}
```

## Referencias

- Eve schema -  http://python-eve.org/config.html#schema
