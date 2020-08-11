# Basic Form

```js
import React from 'react';
import { Link } from 'react-router-dom';

const SubjectForm = ({ subject, handleSubmit, handleChange, cancelPath }) => {

	return (

		<form onSubmit={handleSubmit}>
			<label>Text</label>
			<input placeholder={'Text here'} value={subject.field1} name='field1' onChange={handleChange} />

			<label>Text</label>
			<input placeholder={`Text here`} value={subject.field2} name='field2' onChange={handleChange} />

			<button type="submit">Submit</button>
			<Link to={cancelPath}>
				<button>Cancel</button>
			</Link>
		</form>
    	);
};

export default SubjectForm;
```