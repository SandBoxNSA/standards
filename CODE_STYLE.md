# 🎨 Guía de estilo de código

Stack cubierto: **Node.js (TypeScript preferido)** y **Python**.

> **Regla de oro:** el formato lo decide una **herramienta**, no una persona. Si discutes comas o espacios en un PR, falta configurar el formateador.

---

## 1. Principios generales

| Principio | Significado | Ejemplo corto |
|---|---|---|
| **KISS** | Mantenlo simple. | Una función simple > un patrón de diseño innecesario. |
| **DRY** | No repitas lógica. | Si copias 3 veces el mismo bloque, hazlo función. |
| **YAGNI** | No construyas lo que no necesitas *todavía*. | No crees un sistema de plugins "por si acaso". |
| **SoC** | Cada parte hace una sola cosa. | Separa: acceso a datos, lógica de negocio, interfaz. |
| **Falla rápido** | Detecta errores pronto y con mensajes claros. | Valida argumentos al inicio de la función. |

Además: el código se **lee** muchas más veces de las que se **escribe**. Optimiza para quien lo lee.

---

## 2. Nombres

### Reglas comunes

- **Nombres descriptivos**, sin abreviaturas raras: `totalPrice` ✅, `tp` ❌.
- **Código en inglés** (variables, funciones, clases). **Documentación y mensajes de commit en español** (ver `COMMITS.md`).
- **Booleanos** con prefijo de pregunta: `isActive`, `hasPermission`, `canEdit`.
- **Funciones = verbos**: `calculateTotal()`, `sendEmail()`.
- **Clases/tipos = sustantivos**: `UserRepository`, `OrderService`.
- Evita nombres genéricos (`data`, `info`, `temp`, `manager`) salvo en contextos muy pequeños.

### Tabla de convenciones

| Elemento | TypeScript / JavaScript | Python |
|---|---|---|
| Variables y funciones | `camelCase` | `snake_case` |
| Clases, tipos, interfaces | `PascalCase` | `PascalCase` |
| Constantes | `UPPER_SNAKE_CASE` | `UPPER_SNAKE_CASE` |
| Archivos | `kebab-case.ts` (ej. `user-service.ts`) | `snake_case.py` (ej. `user_service.py`) |
| Carpetas | `kebab-case` | `snake_case` |
| Miembros privados | `#campo` o `private campo` | `_campo` |
| Tests | `user-service.test.ts` | `test_user_service.py` |
| Variables de entorno | `UPPER_SNAKE_CASE` (ej. `DATABASE_URL`) | igual |

---

## 3. Formato y herramientas

### Archivos de configuración compartidos

**`.editorconfig`** (pon este archivo en la raíz de cada proyecto):

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 2

[*.py]
indent_size = 4

[*.md]
trim_trailing_whitespace = false
```

### Node.js / TypeScript

| Herramienta | Para qué |
|---|---|
| **Prettier** | Formato automático. |
| **ESLint** | Detectar errores y malas prácticas. |
| **TypeScript** con `"strict": true` | Tipado estricto. |
| **Vitest** o **Jest** | Pruebas. |
| **pnpm** o **npm** (uno por proyecto, con *lockfile* versionado) | Dependencias. |

`.prettierrc` sugerido:

```json
{
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "printWidth": 100
}
```

Scripts mínimos en `package.json`:

```json
{
  "scripts": {
    "lint": "eslint .",
    "format": "prettier --write .",
    "test": "vitest run",
    "build": "tsc --noEmit"
  }
}
```

### Python

| Herramienta | Para qué |
|---|---|
| **Ruff** | Lint **y** formato (reemplaza flake8, isort y black). |
| **pytest** | Pruebas. |
| **mypy** o **pyright** | Verificación de tipos. |
| **uv** o **venv + pip** | Entornos y dependencias. |

`pyproject.toml` sugerido:

```toml
[tool.ruff]
line-length = 100
target-version = "py312"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "UP", "B", "S"]   # S = reglas de seguridad (bandit)
```

> Usa siempre un **entorno virtual** (`python -m venv .venv`). Nunca instales paquetes globalmente para un proyecto.

---

## 4. Ejemplos

### Nombres claros y funciones pequeñas

```ts
// ❌ Difícil de entender
function calc(a: number[], b: number) {
  let t = 0;
  for (let i = 0; i < a.length; i++) t += a[i];
  return t - t * b;
}

// ✅ Claro
function calculateTotalWithDiscount(prices: number[], discountRate: number): number {
  const subtotal = prices.reduce((sum, price) => sum + price, 0);
  return subtotal * (1 - discountRate);
}
```

```python
# ❌ Difícil de entender
def calc(a, b):
    t = sum(a)
    return t - t * b

# ✅ Claro
def calculate_total_with_discount(prices: list[float], discount_rate: float) -> float:
    subtotal = sum(prices)
    return subtotal * (1 - discount_rate)
```

### Retornos tempranos (evita anidar demasiado)

```ts
// ❌ Muchos niveles
function getDiscount(user: User | null) {
  if (user) {
    if (user.isActive) {
      return user.discount;
    }
  }
  return 0;
}

// ✅ Retorno temprano
function getDiscount(user: User | null): number {
  if (!user || !user.isActive) return 0;
  return user.discount;
}
```

### Manejo de errores

```ts
// ❌ Se traga el error: nadie sabrá que falló
try {
  await saveUser(user);
} catch (e) {}

// ✅ Maneja o propaga con contexto, sin exponer datos sensibles
try {
  await saveUser(user);
} catch (error) {
  logger.error({ err: error, userId: user.id }, 'No se pudo guardar el usuario');
  throw new AppError('USER_SAVE_FAILED', 'No se pudo guardar el usuario');
}
```

```python
# ❌ except desnudo
try:
    save_user(user)
except:
    pass

# ✅ Excepción específica + contexto
try:
    save_user(user)
except DatabaseError as error:
    logger.error("No se pudo guardar el usuario", extra={"user_id": user.id})
    raise UserSaveError("No se pudo guardar el usuario") from error
```

---

## 5. Estructura de proyecto

```text
mi-proyecto/
├── src/
│   ├── modules/          # una carpeta por dominio (users, orders, ...)
│   │   └── users/
│   │       ├── user.controller.ts   # entrada (HTTP, CLI)
│   │       ├── user.service.ts      # lógica de negocio
│   │       └── user.repository.ts   # acceso a datos
│   ├── shared/           # utilidades comunes
│   └── config/           # lectura y validación de variables de entorno
├── tests/
├── docs/
├── .env.example          # variables necesarias, SIN valores reales
├── .editorconfig
├── .gitignore
└── README.md
```

---

## 6. Comentarios y documentación

- **El código dice el "qué"; el comentario dice el "porqué".**
- No comentes lo obvio. Mejor renombra para que sea claro.
- Documenta funciones **públicas** (JSDoc / docstrings) con qué hacen, parámetros y errores.
- Marca deuda técnica con `TODO(#123): descripción` (siempre con número de issue).

```ts
// ❌ Comentario inútil
// suma 1 a i
i++;

// ✅ Explica una decisión no obvia
// Reintentamos 3 veces porque el proveedor de pagos devuelve 503 de forma intermitente.
const MAX_RETRIES = 3;
```

---

## 7. Seguridad en el código (mínimos obligatorios)

- **Valida y sanea toda entrada externa** (formularios, APIs, archivos, variables de entorno). En TS usa **Zod**; en Python, **Pydantic**.
- **Consultas a base de datos parametrizadas** (nunca concatenes strings con datos del usuario).
- **Secretos solo en variables de entorno** o gestor de secretos. Jamás en el código.
- **No registres** contraseñas, tokens ni datos personales en logs.
- **Contraseñas:** hash con Argon2id o bcrypt. Nunca cifrado reversible ni MD5/SHA1.
- **Dependencias:** fija versiones con lockfile, revisa `npm audit` / `pip-audit` en CI.
- **Principio de mínimo privilegio** en permisos de usuarios, tokens y servicios.

---

## 8. Pruebas

- Patrón **Arrange – Act – Assert** (preparar, ejecutar, verificar).
- **Nombres que describen comportamiento:** `debe_rechazar_correo_invalido` / `should reject invalid email`.
- Prueba **casos normales, límites y errores**.
- Un test = una razón para fallar.
- Los tests no dependen unos de otros ni de datos reales de producción.

```ts
describe('calculateTotalWithDiscount', () => {
  it('should apply the discount to the subtotal', () => {
    // Arrange
    const prices = [100, 50];
    // Act
    const total = calculateTotalWithDiscount(prices, 0.1);
    // Assert
    expect(total).toBe(135);
  });
});
```

---

## 9. Checklist antes de abrir un PR

- [ ] `format` y `lint` sin errores
- [ ] Tipos correctos (`tsc` / `mypy`)
- [ ] Tests pasan localmente
- [ ] Sin `console.log` / `print` de depuración
- [ ] Sin secretos ni datos reales
- [ ] Nombres claros y funciones pequeñas
