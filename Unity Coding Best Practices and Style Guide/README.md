# C# Code and Unity Style Conventions

## Code style conventions

Based on [dotnet/runtime's C# coding style](https://github.com/dotnet/runtime/blob/main/docs/coding-guidelines/coding-style.md).

### Golden rule #1

"We want our code to read clearly. Favor clarity over all else."

- It may mean an extra line of code or two…
- …but don't be redundant!
- If your code reads like a sentence in plain English, you're doing it right.
- If it's so clear any comment you add would be redundant, you're doing it right.

### Golden rule #2

"Use consistent style conventions! For things not covered here, once you do
something one way, be consistent. If you figure out a better way later,
refactor!"

### Braces and indentation

Each curly brace goes on its own line, including the braces after a class,
method, `if`, or loop. Indent everything inside a pair of braces by one level
(4 spaces).

```csharp
private void Start()
{
    if (_hasKey)
    {
        UnlockDoor();
    }
}
```

### Use descriptive, accurate names for variables, methods, and classes

- Favor descriptiveness and clarity over brevity (don't abbreviate!).
- An object's name should reflect what it is (a noun).
- A method's name should be a verb that says what it does, and the method
  should only do that.
- A bool's name should be a yes-or-no question (e.g. `_isInvulnerable`,
  `HasKey`).
- Name each script file exactly the same as the class inside it:
  `PlayerMovement.cs` holds `public class PlayerMovement`. Unity can't attach
  a script to a GameObject if the names don't match.
- Name events for what just happened, starting with `On` (e.g.
  `OnDoorOpened`).

### Capitalization

- `_camelCase` for private fields (including `[SerializeField]` fields)
- `camelCase` for local variables and parameters
- `PascalCase` for everything else: classes, methods, properties, constants,
  events, and enums

```csharp
public class PlayerHealth : MonoBehaviour         // class: PascalCase
{
    private const float MaxHealth = 100f;         // constant: PascalCase

    private float _health = MaxHealth;            // private field: _camelCase

    public float Health => _health;               // property: PascalCase

    public void Heal(float amount)                // method: PascalCase, parameter: camelCase
    {
        float healedHealth = _health + amount;    // local variable: camelCase
        _health = Mathf.Min(healedHealth, MaxHealth);
    }
}
```

### Make everything private unless necessary

- Always write the access modifier, even when it's `private`. It makes fields
  stand out from local variables and adds clarity.
  - Good: `private float _walkSpeed;`
  - Bad: `float _walkSpeed;`
- Unity messages like `Awake()`, `Start()`, and `Update()` are called by Unity
  itself, so they're `private` too: `private void Update()`.
- Use `[SerializeField]` to show a field in the Inspector, not the `public`
  access modifier.
  - Good: `[SerializeField] private float _walkSpeed = 3f;`
  - Bad: `public float walkSpeed = 3f;`
- Use a property when another class needs to read a value:
  `public bool HasKey => _hasKey;`
- Methods that a subclass will override are `public virtual` (e.g.
  `public virtual void Interact()` on an `Interactable` base class).

### Use named variables to increase readability

- Avoid magic numbers.
  - Good: `_health -= _attackDamage;`
  - Bad: `_health -= 5;`
- Simplify complex expressions so they read clearly in plain English.

  Good:

  ```csharp
  bool canJump = _isOnGround && !_isDead && _hasJumpBoots;
  if (canJump && _wasJumpPressed)
  {
      Jump();
  }
  ```

  Bad:

  ```csharp
  if (_isOnGround && !_isDead && _hasJumpBoots && _wasJumpPressed)
  {
      Jump();
  }
  ```

### Use summary and Tooltip to document code

- Use a `/// <summary>` comment to document classes and methods. (In Visual
  Studio, typing three forward slashes above a method inserts an empty
  summary for you.)
- Use Unity's `[Tooltip()]` attribute to document fields that show in the
  Inspector (serialized fields). The text appears when you hover over the
  field in the Inspector.
- Use plain comments (`//`) for anything else that might be unclear.
- Remember: if your names and code structure are clear enough, you might not
  need a comment at all. A field named `_shotgunAmmoCount` doesn't need a
  comment explaining it.

```csharp
/// <summary>
/// Opens and closes the door when the player interacts with it.
/// </summary>
public class Door : Interactable
{
    [Tooltip("Seconds the door takes to swing fully open.")]
    [SerializeField] private float _openDuration = 1.5f;
}
```

### List elements in intentional order

Based on [StyleCop rule SA1201](https://github.com/DotNetAnalyzers/StyleCopAnalyzers/blob/master/documentation/SA1201.md).

Order a class's contents like this:

1. **Fields:** serialized fields first, then other private fields
2. Events
3. Enums
4. **Properties**
5. **Methods:**
   1. Unity messages, in the order Unity calls them (`Awake`, `OnEnable`,
      `OnDisable`, `Start`, `Update`, …)
   2. Your public methods
   3. Your private methods

Within a group, put `static` members before non-static ones.

### Remove unused code

- Remove any empty `Start()` or `Update()` that Unity's script template
  created and you didn't use.
- Remove commented-out code.
- Remove unused variables and methods.

### Putting it together

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

/// <summary>
/// Turns the player's flashlight on and off, and drains its battery while it's on.
/// </summary>
public class Flashlight : MonoBehaviour
{
    [Tooltip("The input action that toggles the flashlight.")]
    [SerializeField] private InputActionReference _toggleAction;

    [Tooltip("The Light component this script turns on and off.")]
    [SerializeField] private Light _light;

    [Tooltip("How many seconds a full battery lasts.")]
    [SerializeField] private float _batteryLifeSeconds = 120f;

    private float _batteryRemainingSeconds;

    public bool IsOn => _light.enabled;
    public bool HasBattery => _batteryRemainingSeconds > 0f;

    private void Awake()
    {
        _batteryRemainingSeconds = _batteryLifeSeconds;
    }

    private void OnEnable()
    {
        _toggleAction.action.performed += OnTogglePerformed;
    }

    private void OnDisable()
    {
        _toggleAction.action.performed -= OnTogglePerformed;
    }

    private void Update()
    {
        if (IsOn)
        {
            DrainBattery();
        }
    }

    public void TurnOff()
    {
        _light.enabled = false;
    }

    private void OnTogglePerformed(InputAction.CallbackContext context)
    {
        bool canTurnOn = !IsOn && HasBattery;
        _light.enabled = canTurnOn;
    }

    private void DrainBattery()
    {
        _batteryRemainingSeconds -= Time.deltaTime;
        if (!HasBattery)
        {
            TurnOff();
        }
    }
}
```

## Unity style conventions

1. Organize assets in folders by asset type (Scenes, Scripts, Prefabs,
   Animations, Sprites, Models, Audio, Fonts, Materials, Physics Materials,
   etc.).
2. Any GameObject that's reused should be a prefab.
3. Name GameObjects and assets descriptively, with consistent capitalization.
   If a capsule is your player, name it "Player," not "Capsule1."
4. Use well-named empty GameObjects as "folders" to hide clutter. Organize a
   scene's Hierarchy by location, e.g. First Floor > Kitchen > Walls.
5. Name scenes descriptively.
6. Use the new Input System, not the old Input Manager functions
   (`Input.GetKey`, `Input.GetAxis`, `Input.GetButton`, etc.). Read input
   through Input Actions (an `InputActionReference` or the generated C#
   class), and don't hard-code specific keys like `Keyboard.current.eKey` in
   gameplay scripts.
