# Reglas de base de datos

- Nombres de modelos en singular y CamelCase (ej. Pedido).
- Nombres de campos en snake_case, sin acentos ni enie (anio).
- Toda ForeignKey lleva related_name explicito y on_delete explicito.
- Usa DecimalField para dinero, nunca FloatField.
- Los modelos definen __str__ con un texto legible (nombre, patente,
  codigo...).
- Los textos de opciones (choices) se definen con models.TextChoices.
- Los permisos propios se declaran en Meta.permissions.
- No escribas migraciones a mano: se generan con makemigrations.
