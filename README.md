# deplay-spinner

## NOSQL-DOCUMENT DATABASE
```js

const handleSubmit = async (e) => {
    e.preventDefault();
    const newError = {};

    // Perform validation
    if (!batch) newError.batch = 'Batch cannot be empty';
    if (!courseID) newError.courseID = 'Course ID cannot be empty';
    if (!title) newError.title = 'Title cannot be empty';
    if (videoUrls.some((video) => !video.url))
        newError.videoUrls = 'All video URLs must be filled';
    if (resources.some((resource) => !resource.url))
        newError.resources = 'All resource URLs must be filled';
    if (!description) newError.description = 'Description cannot be empty';
    if (!assessment) newError.assessment = 'Assessment cannot be empty';

    if (Object.keys(newError).length > 0) {
        return setError(newError);
    }

    // Gather all form data
    const formData = {
        assessment: { completed: false, assessment },
        batchID: batch,
        courseID,
        completed: false,
        createdAt: Timestamp.now(),
        description,
        resources: resources.map((resource) => resource.url).filter((url) => url),
        title,
        updatedAt: Timestamp.now(),
        videoURL: videoUrls.map((video) => video.url).filter((url) => url),
    };

    try {
        setIsubmitting(true);
        setError(null);

        const startTime = Date.now(); // Record start time

        // Perform the actual submission
        const docRef = await addLessonToCourse(formData); // caould be fetch() 

        // Calculate elapsed time and ensure spinner shows for at least 3 seconds
        const elapsedTime = Date.now() - startTime;
        if (elapsedTime < 3000) {
            await new Promise((resolve) => setTimeout(resolve, 3000 - elapsedTime));
        }

        toast.success('Lesson added successfully!');
        console.log(docRef);
    } catch (error) {
        setError(error.message);
    } finally {
        setIsubmitting(false);
    }
};



```

## FETCH
```js

import React, { useState } from 'react';
import { toast } from 'react-toastify';

function AddLessonToCourse({ courseId, courseTitle }) {
	const [batch, setBatch] = useState('');
	const [courseID, setCourseID] = useState(courseId);
	const [title, setTitle] = useState('');
	const [videoUrls, setVideoUrls] = useState([{ id: Date.now(), url: '' }]);
	const [resources, setResources] = useState([{ id: Date.now(), url: '' }]);
	const [description, setDescription] = useState('');
	const [assessment, setAssessment] = useState('');
	const [error, setError] = useState('');
	const [isSubmitting, setIsubmitting] = useState(false);

	const handleVideoUrlChange = (id, value) => {
		setVideoUrls((prev) =>
			prev.map((video) => (video.id === id ? { ...video, url: value } : video))
		);
	};

	const addVideoUrl = () => {
		setVideoUrls([...videoUrls, { id: Date.now(), url: '' }]);
	};

	const removeVideoUrl = (id) => {
		setVideoUrls((prev) => prev.filter((video) => video.id !== id));
	};

	const handleResourceChange = (id, value) => {
		setResources((prev) =>
			prev.map((resource) =>
				resource.id === id ? { ...resource, url: value } : resource
			)
		);
	};

	const addResource = () => {
		setResources([...resources, { id: Date.now(), url: '' }]);
	};

	const removeResource = (id) => {
		setResources((prev) => prev.filter((resource) => resource.id !== id));
	};

	const handleSubmit = async (e) => {
		e.preventDefault();
		const newError = {};

		// Perform validation
		if (!batch) newError.batch = 'Batch cannot be empty';
		if (!courseID) newError.courseID = 'Course ID cannot be empty';
		if (!title) newError.title = 'Title cannot be empty';
		if (videoUrls.some((video) => !video.url))
			newError.videoUrls = 'All video URLs must be filled';
		if (resources.some((resource) => !resource.url))
			newError.resources = 'All resource URLs must be filled';
		if (!description) newError.description = 'Description cannot be empty';
		if (!assessment) newError.assessment = 'Assessment cannot be empty';

		if (Object.keys(newError).length > 0) {
			return setError(newError);
		}

		// Gather all form data
		const formData = {
			assessment: { completed: false, assessment },
			batchID: batch,
			courseID,
			completed: false,
			createdAt: Date.now(),
			description,
			resources: resources.map((resource) => resource.url).filter((url) => url),
			title,
			updatedAt: Date.now(),
			videoURL: videoUrls.map((video) => video.url).filter((url) => url),
		};

		try {
			setIsubmitting(true);
			setError(null);

			const startTime = Date.now(); // Record start time

			// Perform the actual submission
			const response = await fetch('http://localhost/add', {
				method: 'POST',
				headers: { 'Content-Type': 'application/json' },
				body: JSON.stringify(formData),
			});
			const docRef = await response.json();

			// Calculate elapsed time and ensure spinner shows for at least 3 seconds
			const elapsedTime = Date.now() - startTime;
			if (elapsedTime < 3000) {
				await new Promise((resolve) => setTimeout(resolve, 3000 - elapsedTime));
			}

			toast.success('Lesson added successfully!');
			console.log(docRef);
		} catch (error) {
			setError(error.message);
		} finally {
			setIsubmitting(false);
		}
	};

	return (
		<form
			className='bg-white p-6 rounded-md border border-slate-100 mt-6 w-full max-w-[512px] space-y-6'
			onSubmit={handleSubmit}
		>
			{/* Select Batch */}
			<div className='flex flex-col space-y-2'>
				<label className='text-xs'>Select Batch</label>
				<select
					className='px-3 h-8 bg-white border  border-slate-100 rounded-md outline-none'
					value={batch}
					onChange={(e) => setBatch(e.target.value)}
				>
					<option value=''>--Select Batch--</option>
					<option value='batchA2025'>batchA2025</option>
					<option value='batchB2025'>batchB2025</option>
				</select>
				{error.batch && (
					<span className='text-red-500 text-sm'>{error?.batch}</span>
				)}
			</div>

			{/* Course ID */}
			<div className='flex flex-col space-y-2'>
				<label className='text-xs'>Course ID</label>
				<select
					className='px-3 h-8 bg-white border  border-slate-100 rounded-md outline-none'
					value={courseID}
					onChange={(e) => setCourseID(e.target.value)}
				>
					<option value={courseID}>{courseID}</option>
				</select>
				{error.courseID && (
					<span className='text-red-500 text-sm'>{error?.courseID}</span>
				)}
			</div>

			{/* Lesson Title */}
			<div className='flex flex-col space-y-2'>
				<label className='text-xs'>Title</label>
				<input
					type='text'
					value={title}
					onChange={(e) => setTitle(e.target.value)}
					placeholder='Enter lesson title'
					className='px-3 h-8 border border-slate-100 rounded-md outline-none'
				/>
				{error.title && (
					<span className='text-red-500 text-sm'>{error?.title}</span>
				)}
			</div>

			{/* Dynamic Video URLs */}
			<div className='flex flex-col space-y-2'>
				<label className='text-xs'>Video URLs</label>
				{videoUrls.map((video) => (
					<div key={video.id}>
						<div className='flex items-end space-x-2 border rounded-md p-4'>
							<input
								type='url'
								value={video.url}
								onChange={(e) => handleVideoUrlChange(video.id, e.target.value)}
								placeholder='Enter video URL'
								className='px-3 h-8 border w-full border-slate-100 rounded-md outline-none'
							/>
							<button
								type='button'
								onClick={() => removeVideoUrl(video.id)}
								className='px-3 h-8 bg-slate-500 text-white font-bold rounded-md'
							>
								Remove
							</button>
						</div>
						{error.videoUrls && (
							<p className='text-red-500 text-sm'>{error?.videoUrls}</p>
						)}
					</div>
				))}
				<button
					type='button'
					onClick={addVideoUrl}
					className='px-3 h-8 bg-slate-950 text-white font-bold rounded-md'
				>
					Add Video URL
				</button>
			</div>

			{/* Dynamic Resources */}
			<div className='flex flex-col space-y-2'>
				<label className='text-xs'>Resources</label>
				{resources.map((resource) => (
					<div key={resource.id}>
						<div className='flex items-end space-x-2 border rounded-md p-4'>
							<input
								type='url'
								value={resource.url}
								onChange={(e) =>
									handleResourceChange(resource.id, e.target.value)
								}
								placeholder='Enter resource URL'
								className='px-3 h-8 border w-full border-slate-100 rounded-md outline-none'
							/>
							<button
								type='button'
								onClick={() => removeResource(resource.id)}
								className='px-3 h-8 bg-slate-500 text-white font-bold rounded-md'
							>
								Remove
							</button>
						</div>
						{error.resources && (
							<p className='text-red-500 text-sm'>{error?.resources}</p>
						)}
					</div>
				))}
				<button
					type='button'
					onClick={addResource}
					className='px-3 h-8 bg-slate-950 text-white font-bold rounded-md'
				>
					Add Resource
				</button>
			</div>

			{/* Lesson Description */}
			<div className='flex flex-col space-y-2'>
				<label className='text-xs'>Description</label>
				<textarea
					value={description}
					onChange={(e) => setDescription(e.target.value)}
					placeholder='Enter lesson description'
					className='px-3 border border-slate-100 rounded-md outline-none'
				></textarea>
				{error.description && (
					<span className='text-red-500 text-sm'>{error?.description}</span>
				)}
			</div>

			{/* Assessment */}
			<div className='flex flex-col space-y-2'>
				<label className='text-sm font-medium'>Assessment (Optional)</label>
				<textarea
					name='assessment'
					value={assessment}
					onChange={(e) => setAssessment(e.target.value)}
					placeholder='Enter assessment details'
					className='px-3 py-2 h-24 border border-slate-200 rounded-md outline-none focus:border-slate-400 transition-colors resize-y'
				/>
				{error.assessment && (
					<span className='text-red-500 text-sm'>{error?.assessment}</span>
				)}
			</div>
			<div className='pt-10 flex justify-end'>
				<button
					type='submit'
					className='px-3 h-8 bg-slate-950 text-white font-bold rounded-md flex items-center justify-center'
					disabled={isSubmitting}
				>
					{isSubmitting ? (
						<div className='loader'></div>
					) : (
						'Add Lesson'
					)}
				</button>
			</div>
		</form>
	);
}

// Add CSS for the loader
const loaderStyle = document.createElement('style');
loaderStyle.innerHTML = `
	.loader {
		border: 4px solid #f3f3f3;
		border-top: 4px solid #3498db;
		border-radius: 50%;
		width: 16px;
		height: 16px;
		animation: spin 2s linear infinite;
	}

	@keyframes spin {
		0% { transform: rotate(0deg); }
		100% { transform: rotate(360deg); }
	}
`;
document.head.appendChild(loaderStyle);

export default AddLessonToCourse;

```
