# SIMPLE COLUMNS IN MDX

column components

```
/** @jsx jsx */
import { css, jsx } from '@emotion/react'

export const ThirtySeventy = ({children}) => (
    <div
        css={{
            display: "grid",
            "grid-template-columns": "3fr 7fr"
        }}
    >
        {children}
    </div>
)

export const Thirty = ({children}) => {
    return (
        <div>
            {children}
        </div>
    )
}

export const Seventy = ({children}) => {
    return (
        <div>
            {children}
        </div>
    )
}

export const StickyOne = ({children}) => {
    return (
        <div
            css={{
                position: "sticky",
                top: "50%",
                padding: "10px",
                backgroundColor: "rgba(0, 0, 20, 0.2)"
            }}
        >
            {children}
        </div>
    )
}
```

then mdx

```
import { ThirtySeventy, Thirty, Seventy, StickyOne } from "../components/layouts/columns"

<ThirtySeventy>
<Thirty>

<StickyOne>

# MDX
Testing testing.

</StickyOne>
</Thirty>
<Seventy>

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Phasellus imperdiet, nulla et dictum interdum, nisi lorem egestas odio, vitae scelerisque enim ligula venenatis dolor. Maecenas nisl est, ultrices nec congue eget, auctor vitae massa. Fusce luctus vestibulum augue ut aliquet. Mauris ante ligula, facilisis sed ornare eu, lobortis in odio. Praesent convallis urna a lacus interdum ut hendrerit risus congue. Nunc sagittis dictum nisi, sed ullamcorper ipsum dignissim ac. I
```

etc.
