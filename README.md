# Role base access control in React

### Code snippets for React JS 

* Create a button component which can check loggedin user role and depending on the role it can show different UI states to the user.
```
type Props = {
    action: string;
    path?: any;
    children?: any;
};
const ProtectedButton = (props: Props) => {
  const { isActionAccessible } = usesPermissions('', props.action);
  return (
        <>
            {isActionAccessible ? (
                <Button onClick={doSomething}>{props.children}</Button>
            ) : (
                <Button disabled={!isActionAccessible}>{props.children}</Button>
            )}
        </>
    );
};
export default ProtectedButton;
```

* Create rules that define which role can perform any action. Simplest implementation for this is to define roles and action map. For Example, an Admin user can create, edit, and delete data. 

- Create Rules
```
const rules: any = {
    admin: {
        actions: [
            actions.addService,
            actions.deployService,
            actions.editService,
            actions.viewService
        ],
    },
    editor: {
        actions: [
            actions.addService,
            actions.editService,
            actions.viewService
        ],
    },
    viewer: {
        actions: [actions.viewService],
    },
};
```
- Create a method that can get current user role and check whether this user can access an component or not.
```
export const usesPermissions = (action?: string) => {
    //Get current suer role
    const { role } = getCurrentUser();
    //get selected role from rules
    const selectedRule = rules[role];
    let isActionAccessible = false;
    if (selectedRule) {
        if (action) {
            //check selected rule can access given action
            const selectedAction = selectedRule.actions;
            isActionAccessible = selectedAction.includes(action);
        }
    }
    return { isActionAccessible };
};
```

- Define actions
```
export const actions = {
    addService: 'add_service',
    deployService: 'deploy_services',
    editService: 'edit_service',
    viewService: 'view_service'
};
```

This approach is pretty generic and can be applied to any component, such as textfields, dropdowns and even routes.

* Consume the component
  ```
  <ProtectedButton
    action={actions.addService}
    path={links.createUser.path}
  >
    Add User
  </ProtectedButton>
  ```

