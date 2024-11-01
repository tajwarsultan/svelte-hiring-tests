<script lang="ts">
  import { writable } from "svelte/store";

  // TODO: Implement form state management
  // TODO: Implement form validation
  // TODO: Implement submit handler
  // TODO: Implement success state management

  type FormStateType = Record<
    keyof App.UserFormData,
    { value: string; touched: boolean; error: string }
  >;

  const validators: Record<keyof App.UserFormData, (value: string) => string> =
    {
      firstName: (value) => (value ? "" : "First Name is required"),
      lastName: (value) => (value ? "" : "Last Name is required"),
      password: (value) => {
        if (!value) return "Password is required";
        return value.length >= 6
          ? ""
          : "Password must be at least 6 characters";
      },
      email: (value) => {
        if (!value) return "Email is required";
        return /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/.test(value)
          ? ""
          : "Please enter valid Email";
      },
    };

  const FormInitaialState = (): FormStateType => ({
    firstName: { value: "", touched: false, error: "" },
    lastName: { value: "", touched: false, error: "" },
    email: { value: "", touched: false, error: "" },
    password: { value: "", touched: false, error: "" },
  });

  const FormState = writable<FormStateType>(FormInitaialState());
  const isSubmitted = writable(false);
  const submittedData = writable<App.UserFormData | null>(null);

  const updateField = (field: keyof FormStateType, value: string) => {
    FormState.update((state) => ({
      ...state,
      [field]: {
        value,
        touched: true,
        error: validators[field](value),
      },
    }));
    isSubmitted.set(false);
  };

  const handelSubmit = (e: Event) => {
    e.preventDefault();
    FormState.update((state) => {
      const updatedState = { ...state };
      (Object.keys(state) as Array<keyof FormStateType>).forEach((field) => {
        updatedState[field] = {
          ...state[field],
          touched: true,
          error: validators[field](state[field].value),
        };
      });

      isSubmitted.set(false);
      return updatedState;
    });

    let currentState: FormStateType;

    FormState.subscribe((state) => (currentState = state))();

    const hasErrors = Object.values(currentState).some((field) => field.error);

    if (!hasErrors) {
      const formDatatoSubmit = Object.entries(currentState).reduce(
        (acc, [key, field]) => ({
          ...acc,
          [key]: field.value,
        }),
        {} as App.UserFormData
      );

      submittedData.set(formDatatoSubmit);
      isSubmitted.set(true);
      FormState.set(FormInitaialState());
    }
  };
</script>

<div class="form-container">
  <form on:submit={handelSubmit}>
    <div class="form-group">
      <label for="firstName">First Name</label>
      <input
        on:input={(e) => updateField("firstName", e.currentTarget.value)}
        value={$FormState.firstName.value}
        class:error={$FormState.firstName.touched && $FormState.firstName.error}
        id="firstName"
        type="text"
        placeholder="Enter your first name"
      />
      {#if $FormState.firstName.touched && $FormState.firstName.error}
        <span class="error-message">{$FormState.firstName.error}</span>
      {/if}
    </div>

    <div class="form-group">
      <label for="lastName">Last Name</label>
      <input
        on:input={(e) => updateField("lastName", e.currentTarget.value)}
        value={$FormState.lastName.value}
        id="lastName"
        type="text"
        placeholder="Enter your last name"
        class:error={$FormState.lastName.touched && $FormState.lastName.error}
      />

      {#if $FormState.lastName.touched && $FormState.lastName.error}
        <span class="error-message">{$FormState.lastName.error}</span>
      {/if}
    </div>

    <div class="form-group">
      <label for="email">Email</label>
      <input
        on:input={(e) => updateField("email", e.currentTarget.value)}
        value={$FormState.email.value}
        id="email"
        type="email"
        placeholder="Enter your email"
        class:error={$FormState.email.touched && $FormState.email.error}
      />
      {#if $FormState.email.touched && $FormState.email.error}
        <span class="error-message">{$FormState.email.error}</span>
      {/if}
    </div>

    <div class="form-group">
      <label for="password">Password</label>
      <input
        on:input={(e) => updateField("password", e.currentTarget.value)}
        value={$FormState.password.value}
        id="password"
        type="password"
        placeholder="Enter your password"
        class:error={$FormState.password.touched && $FormState.password.error}
      />
      {#if $FormState.password.touched && $FormState.password.error}
        <span class="error-message">{$FormState.password.error}</span>
      {/if}
    </div>

    <button type="submit" class="submit-button">Submit</button>
  </form>

  <!-- TODO: Add success message section here -->
  {#if $isSubmitted && $submittedData}
    <div class="success-message">
        <p><strong>Frist Name :</strong> {$submittedData.firstName}</p>
        <p><strong>Last Name :</strong> {$submittedData.lastName}</p>
        <p><strong>Email :</strong> {$submittedData.email}</p>
    </div>
  {/if}
</div>

<style>
  .form-container {
    max-width: 480px;
    margin: 0 auto;
    padding: 2rem;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .form-group {
    margin-bottom: 1.5rem;
  }

  label {
    display: block;
    margin-bottom: 0.5rem;
    font-weight: 500;
    color: #374151;
  }

  input {
    width: 100%;
    padding: 0.75rem;
    border: 1px solid #d1d5db;
    border-radius: 6px;
    font-size: 1rem;
    transition: border-color 0.15s ease-in-out;
  }

  input:focus {
    outline: none;
    border-color: #3b82f6;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
  }

  input::placeholder {
    color: #9ca3af;
  }

  input.error {
    border-color: #ef4444;
  }

  .error-message {
    display: block;
    margin-top: 0.5rem;
    font-size: 0.875rem;
    color: #ef4444;
  }

  .submit-button {
    width: 100%;
    padding: 0.75rem 1.5rem;
    background-color: #3b82f6;
    color: white;
    border: none;
    border-radius: 6px;
    font-size: 1rem;
    font-weight: 500;
    cursor: pointer;
    transition: background-color 0.15s ease-in-out;
  }

  .submit-button:hover {
    background-color: #2563eb;
  }

  .submit-button:focus {
    outline: none;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.5);
  }

  .submit-button:disabled {
    background-color: #9ca3af;
    cursor: not-allowed;
  }

  .success-message {
    margin-top: 1.5rem;
    padding: 1rem;
    background-color: #edf7ed;
    border: 1px solid #c8e6c9;
    border-radius: 6px;
    color: #1b5e20;
  }
</style>
