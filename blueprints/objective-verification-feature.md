It's time to formally define the behavior of the long-awaited platform feature for incorporating optional objectives into lessons. This issue will be used to hash out the details for how this will work, as well as to house discussions and gather feedback.

## Learner Experience

For the learner, this will manifest itself fairly simply. Each stage may or may not contain optional objectives for them to perform. This adds a layer of activity beyond the current "next, next, next..." approach of the current lessons, and inspires the learner to actually DO something. I imagine having this feature in place will allow us to create lessons that are essentially half-finished - the idea being that the learner would complete a fabric configuration, or remediate a problem, etc.

To indicate that a given stage includes optional objectives, there will be some kind of button in the lesson UI that will indicate something like "0/3 objectives completed". When clicked, this button will pop up a modal box that explains each objective, and will contain a green check mark next to any objectives that are completed. At a glance, either at the button, or at this dialog, the user should be able to see what they still need to do.

When all objectives are completed, the button will glow or flash indicating full completion. The aforementioned dialog will show a message that is defined by the lesson author in the lesson metadata. We could also provide buttons for sharing the accomplishment via social. In the future, additional integrations with external systems may also be possible, but for the initial feature, we'll leave it at this.

## Lesson Author Experience

Objectives are defined per-stage. So, at the very minimum, if a lesson author wants to take advantage of this feature, they need to add the `objectives` field as below. This field should have a list of strings for a value, and that list must be greater than 0. If the author doesn't want to use this feature, just leave the field out (this is what's true today).

```yaml
stages:
  - id: 1
    description: Stage 1
    verification:
      objectives:
      - The vqfx must have BGP configured
      - The linux endpoint must have a text file in the antidote user directory called "foobar"
      completeMessage: Congrats!
```

The presence of a nonzero list of `objective` entries will cause syringe to perform additional checks on the lesson directory. Each stage that uses this feature needs to have a `objectives` directory, and within that directory must be a Python script for checking if that objective is met within the lesson environment.

![Screenshot from 2019-09-17 11-38-26](https://user-images.githubusercontent.com/4230395/65069517-ad968280-d93f-11e9-949d-7dcfe0239894.png)

This is true of any stage that uses this feature. The lesson import functionality in both `syringed` and `syrctl` will be augmented to incorporate automated checks to ensure the right files are in place.

## Back-End Implementation

As with most major platform features, changes to both Syringe and Antidote-web will be necessary. The latter should be fairly easy, so I'll start there.

### Antidote-web

The status of each objective will be added as a field to the existing livelesson API, so from a front-end perspective, we just need to periodically check the livelesson API and update any front-end UI elements as described above in the "learner experience" section accordingly.

We'll need a new button, and a new dialog to contain the objective statuses. These will be hidden by default,
and shown if the livelesson API contains any objective statuses, since this is the indication that they're
defined in the lesson stage.

### Syringe

> The way we track state here will change when we get to MP1, so this is intentionally kept high-level.

At the beginning of each stage, Syringe will spin up a pod with the `objective-checker` (this is an image we'll define in https://github.com/nre-learning/antidote-images) image for every optional objective defined in the stage. We'll also want to make sure we kill any old checker pods.

This pod will run the corresponding Python script in the lesson directory. So for the pod spun up for stage 1 objective 2, we'll execute `stage1/objectives/objective1.py` in a goroutine. This will repeat for every objective. This goroutine will be responsible for either periodically executing this script, or the script will be designed to loop on its own, and stream results back to the goroutine. In either case, we'll update the in-memory livelesson state accordingly so that when the front-end queries for status, it will be there.

Each objective will have one of three statuses:

- Incomplete
- Complete
- Error

I think we should also shut down all verification pods the moment all objectives are verified.
