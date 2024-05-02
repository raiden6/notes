# Button

REF
- https://mui.com/material-ui/react-button/

## Wrapper function
```
import React from 'react';
import MuiButton, { ButtonProps } from '@mui/material/Button';

interface WrapperButtonProps extends ButtonProps {
  children: ReactNode;
}

const Button: React.FC<WrapperButtonProps> = ({children, ...rest}) => {
  return (
    <MuiButton {...rest}>
      {children}
    </MuiButton>
  )
}

export default Button;
```


## Usage
```
<Button
  color='primary' size='small' variant='text' onClick={} type='button'
>
  Submit
</Button>
```
