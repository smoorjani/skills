# Feedback collection

Use when implementing authorized user ratings/comments tied to a concrete trace. Capture the active trace ID inside the request (`mlflow.get_current_active_span().trace_id` in Python; `mlflow.getCurrentActiveSpan()?.traceId` in TypeScript) and return/store it for later feedback. Do not confuse it with a run ID.

## Record the judgment and source

Check the installed SDK supports these public APIs:

```python
import mlflow
from mlflow.entities import AssessmentSource

mlflow.log_feedback(
    trace_id=trace_id, name="user_rating", value=rating,
    source=AssessmentSource(source_type="HUMAN", source_id=reviewer_id),
)
```

The published TypeScript SDK (`@mlflow/core` 0.4.0) does not expose `logFeedback` or an assessment-write API. Collect the trace ID, rating and reviewer identity in the TypeScript application, then send them to your application backend to record with Python `mlflow.log_feedback` above. Do not use `mlflow.logFeedback`; it is not a supported API.

Values may be numeric ratings, text comments or boolean judgments. Preserve actual source identity and meaning: HUMAN for user/manual review, LLM_JUDGE for automated model judgments, CODE for programmatic checks. Do not label generated feedback as human. A zero/false value is not a logging failure; a scorer error is not a negative judgment.

Expected-output labels are expectations, not feedback judgments. For requested scorer alignment or Review App labeling use `mlflow-evaluation` when available. For feedback field/source compatibility use the [entity API](https://mlflow.org/docs/latest/api_reference/python_api/mlflow.entities.html) and the installed SDK APIs. Verify the stored assessment belongs to the intended trace and retains value/source; recording user complaints during diagnosis still requires authorization.
