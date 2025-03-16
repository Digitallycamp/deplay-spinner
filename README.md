# deplay-spinner

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
