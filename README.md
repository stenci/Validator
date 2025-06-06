# Yet Another Validator and Formatter for Anvil

This repository contains a flexible validator and formatter for Anvil apps, inspired by [this Anvil forum post](https://anvil.works/forum/t/yet-another-validator-and-formatter/10607) and implemented in Python for use in Anvil client code.

Original idea and discussion: [Anvil Forum Post](https://anvil.works/forum/t/yet-another-validator-and-formatter/10607)

## Features

- **Component Validation**: Attach validation logic to any Anvil component.
- **Custom Error Messages**: Show error messages in a label or as a popover.
- **Flexible Formatting**: Format input fields (e.g., phone numbers, floats, regex-based formatting).
- **Event-driven**: Trigger validation on specified events (e.g., `lost_focus`, `change`).
- **Reusable**: Easily add new validation rules and formats.

## Usage

### Validator Class

The `Validator` class allows you to define validation rules for your Anvil components. You can specify:

- **component**: The Anvil component to validate (always required).
- **error_label**: (Optional) A label to display error messages. If not provided, a popover is shown.
- **message**: (Optional) Custom error message.
- **events**: (Optional) List of events that trigger validation. If not provided, validation is only executed on demand.
- **format**: (Optional) Formatting to apply on `lost_focus`. Supported formats:
  - `phone`: Formats a ten-digit string into `(123) 123-1234`.
  - `float 0.2f`: Converts the value to float and formats it using Python's string formatting.
  - `$`: Equivalent to `float 0.2f`.
  - `regex@pattern@repl`: Applies a regex substitution for formatting.
- **placement**: (Optional) Popover position (`right`, `left`, `top`, `bottom`). Default is `left`.

### Example

```python
from Validator.validator import Validator

  def __init__(self, **properties):
    self.init_components(**properties)

    self.validator = Validator()
    self.validator.between(component=self.text_box_1,
                           events=['lost_focus', 'change'],
                           min_value=5,
                           max_value=10,
                           include_min=True,
                           include_max=False)
    self.validator.number(component=self.text_box_8,
                          events=['lost_focus', 'change'],
                          format='float 07.3f')
    self.validator.with_function(component=self.text_box_13,
                                 events=['lost_focus', 'change'],
                                 validating_function=lambda tb: tb.text >= 'c',
                                 message='Must be greater than "c"')

  def check_all_click(self, **event_args):
    print(self.validator.are_all_valid())
```

### Validator Parameters Explained

The `Validator` class provides a flexible way to attach validation and formatting logic to Anvil components. Here are the key parameters you can use when defining validation rules:

- **component** *(required)*:  
  The Anvil component (e.g., `TextBox`, `DatePicker`) to which the validation rule applies.

- **error_label** *(optional)*:  
  A label component to display error messages. If provided, the label will be shown or hidden automatically based on validation results. If not provided, error messages are displayed as popovers on the component.

- **message** *(optional)*:  
  A custom error message to display when validation fails. If omitted, a default message is used.

- **events** *(optional)*:  
  A list of event names (such as `lost_focus`, `change`) that trigger validation. If not specified, validation is only performed when explicitly requested (e.g., via `validate(component)` or `validate_all()`).

- **format** *(optional)*:  
  Specifies formatting to apply when the component loses focus. Supported formats include:
  - `phone`: Formats a ten-digit string as `(123) 123-1234`.
  - `float 0.2f`: Converts the value to a float and formats it using Python's string formatting syntax.
  - `$`: Equivalent to `float 0.2f`.
  - `regex@pattern@repl`: Applies a regex substitution for formatting. The character after `regex` is used as a separator; choose one not present in the pattern or replacement. For example, `regex@(\d{3}).*(\d{3}).*(\d{4})@(\1) \2-\3` formats a phone number.

- **placement** *(optional)*:  
  Determines the position of the popover when `error_label` is not provided. Options are `right`, `left`, `top`, or `bottom`. The default is `left`.

For a full implementation and additional details, see [`client_code/validator.py`](client_code/validator.py).

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
