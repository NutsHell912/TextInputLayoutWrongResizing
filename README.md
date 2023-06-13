# [TextInputLayout] endIcon prevents TextInputLayout resizing after clearing multiline text

1) setup TextInputLayout with app:endIconMode="clear_text" and app:endIconDrawable="@android:drawable/ic_notification_clear_all"
2) insert or print long text without new line separator (for instance "aaaaaaaaaaaaa" or "aa dfg sdf" multiple times), until multiple wrapping to the next line
3) clear focus from TextInputEditText/TextInputLayout
4) focus on TextInputEditText again and clear all text(via endIcon or software keyboard)

Expected result - TextInputLayout is resizing to its initial height.

Actual result - TextInputLayout is stuck with big height.

![TextInputLayoutWrongResize](https://github.com/material-components/material-components-android/assets/23400995/9e28667f-9ac0-4b34-ac35-09d6e2bbbc7e)

Problem happens only if TextInputLayout is losing focus. Otherwise it's resizing correctly.
After some research I figure out that after losing focus editText has wrong mMinHeight.
As far as I understand, problem is in `private boolean updateEditTextHeightBasedOnIcon` in TextInputLayout.java:
```
// We need to make sure that the EditText's height is at least the same as the end or start
// icon's height (whichever is bigger). This ensures focus works properly, and there is no
// visual jump if the icon is enabled/disabled.
int maxIconHeight = Math.max(endLayout.getMeasuredHeight(), startLayout.getMeasuredHeight());
if (editText.getMeasuredHeight() < maxIconHeight) {
editText.setMinimumHeight(maxIconHeight);
return true;
}
```
