---
title: sqlmodel define multiple relationships between two tables
date: 2024-07-02 12:00:00 +0800
categories: [python,sqlModel]
tags: [python, orm, sqlModel]
---

## sqlalchemy.exc.AmbiguousForeignKeysError: Could not determine join condition between parent/child tables on relationship

### applied situation

when we have two tables, and there are two relationships field defined within. if primary key is not speficied in the relationship, sqlalchemy will raise an error, since it cannot determine which foreign key to use.

This error occurs when there are multiple foreign keys between two tables and sqlalchemy is unable to determine which one to use.

To fix this error, we need to specify which foreign key to use in the relationship.

```python   
class User(SQLModel, table=True):
    #  other fields here
    documents_created: list["Document"] = Relationship(
        back_populates="created_by",
        sa_relationship_kwargs={
            "primaryjoin": "Document.created_id==User.id",
            "lazy": "joined",
        },
    )

    documents_modified: list["Document"] = Relationship(
        back_populates="modified_by",
        sa_relationship_kwargs={
            "primaryjoin": "Document.created_id==User.id",
            "lazy": "joined",
        },
    )


class Document(SQLModel, table=True):
    """Long form document model"""
    # other fields here ...
    created_by: "User" = Relationship(
        back_populates="documents_created",
        sa_relationship_kwargs=dict(foreign_keys="[Document.created_id]"),
    )
    modified_by: "User" = Relationship(
        back_populates="documents_modified",
        sa_relationship_kwargs=dict(foreign_keys="[Document.modified_id]"),
    )
```